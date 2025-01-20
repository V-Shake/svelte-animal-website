<script>
  import { createEventDispatcher, onMount } from "svelte";

  export let mode = "easy"; // Default to easy mode if no mode is passed
  export let amount = 5; // Default question amount if no amount is passed
  export let timerEnabled = false; // Timer toggle state
  let questions = [];
  let currentQuestionIndex = 0;
  let selectedAnswer = null;
  let isCorrect = null;
  let sessionToken = null; // Store the session token
  let isLoading = true; // Show loading state while fetching questions
  let userAnswers = []; // Track user answers for the overview
  let quizCompleted = false; // Flag to show the overview after the last question
  let timer = 30; // Timer countdown in seconds
  let timerInterval;
  let jokerUsed = false; // Track if the joker has been used
  let remainingAnswers = []; // Track the remaining answers after using the joker

  const dispatch = createEventDispatcher();

  // Fetch a new session token
  async function fetchSessionToken() {
    try {
      const response = await fetch("https://opentdb.com/api_token.php?command=request");
      const data = await response.json();
      sessionToken = data.token;
    } catch (error) {
      console.error("Failed to fetch session token:", error);
    }
  }

  // Fetch questions based on the selected mode, amount, and session token
  async function fetchQuestions(retryCount = 0) {
    if (!sessionToken) {
      console.error("Session token is missing. Unable to fetch questions.");
      return;
    }

    try {
      const response = await fetch(
        `https://opentdb.com/api.php?amount=${amount}&category=27&difficulty=${mode}&token=${sessionToken}`
      );

      if (response.status === 429) {
        if (retryCount < 5) {
          console.log(`Rate limit exceeded. Retrying in ${retryCount + 1} seconds...`);
          await new Promise(resolve => setTimeout(resolve, (retryCount + 1) * 1000));
          await fetchQuestions(retryCount + 1);
        } else {
          console.error("Rate limit exceeded. Please try again later.");
        }
        return;
      }

      const data = await response.json();

      if (data.response_code === 4) {
        // Token exhausted, reset it
        console.log("Exhausted all questions. Resetting session token.");
        await resetSessionToken();
        await fetchQuestions(); // Retry after resetting the token
        return;
      }

      if (data.results) {
        questions = data.results.map((q) => ({
          ...q,
          answers: shuffleArray([...q.incorrect_answers, q.correct_answer]),
        }));
      } else {
        console.error("No questions available.");
      }
      isLoading = false;
    } catch (error) {
      console.error("Failed to fetch questions:", error);
    }
  }

  // Reset the session token
  async function resetSessionToken() {
    if (!sessionToken) return;
    try {
      await fetch(`https://opentdb.com/api_token.php?command=reset&token=${sessionToken}`);
    } catch (error) {
      console.error("Failed to reset session token:", error);
    }
  }

  // Shuffle answers for randomness
  function shuffleArray(array) {
    return array.sort(() => Math.random() - 0.5);
  }

  // Handle answer selection
  function handleAnswerSelection(answer) {
    selectedAnswer = answer;
    isCorrect = answer === questions[currentQuestionIndex].correct_answer;

    // Record the user's answer and whether it was correct
    userAnswers = [
      ...userAnswers,
      {
        question: questions[currentQuestionIndex].question,
        selectedAnswer: answer,
        correctAnswer: questions[currentQuestionIndex].correct_answer,
        isCorrect,
      },
    ];

    // Move to the next question
    if (timerEnabled) {
      clearInterval(timerInterval);
    }
  }

  // Move to the next question
  function nextQuestion(skipped = false) {
    if (skipped) {
      // Record the skipped question
      userAnswers = [
        ...userAnswers,
        {
          question: questions[currentQuestionIndex].question,
          selectedAnswer: "You skipped",
          correctAnswer: questions[currentQuestionIndex].correct_answer,
          isCorrect: false,
        },
      ];
    }

    if (currentQuestionIndex < questions.length - 1) {
      currentQuestionIndex++;
      selectedAnswer = null;
      isCorrect = null;
      remainingAnswers = questions[currentQuestionIndex].answers;

      // Reset timer for the next question
      if (timerEnabled) {
        startTimer();
      }
    } else {
      quizCompleted = true; // Show the overview
      clearInterval(timerInterval); // Stop the timer
    }
  }

  // Restart the quiz with the same options
  async function playAgain() {
    currentQuestionIndex = 0;
    selectedAnswer = null;
    isCorrect = null;
    userAnswers = [];
    quizCompleted = false;
    jokerUsed = false;
    isLoading = true;
    await fetchQuestions(); // Fetch new questions
    remainingAnswers = questions[currentQuestionIndex].answers;

    if (timerEnabled) {
      startTimer(); // Start the timer if enabled
    }
  }

  // Navigate to the new game (Quiz.svelte)
  function newGame() {
    dispatch("newGame"); // Emit the newGame event
  }

  // Helper function to create an array of the desired length
  function createArray(length) {
    return Array.from({ length });
  }

  // Start the timer countdown
  function startTimer() {
    timer = 30;
    timerInterval = setInterval(() => {
      timer--;
      if (timer <= 0) {
        clearInterval(timerInterval);
        nextQuestion(true); // Move to the next question and mark it as skipped
      }
    }, 1000);
  }

  // Use the 50:50 Joker
  function useJoker() {
    if (jokerUsed) return;

    jokerUsed = true;
    const correctAnswer = questions[currentQuestionIndex].correct_answer;
    const incorrectAnswers = questions[currentQuestionIndex].answers.filter(answer => answer !== correctAnswer);

    if (incorrectAnswers.length > 1) {
      // Mark two wrong options as grey and move them to the bottom
      remainingAnswers = [correctAnswer, incorrectAnswers[0], ...incorrectAnswers.slice(1).map(answer => ({ answer, isGreyedOut: true }))];
    } else {
      // Mark one wrong option as grey (for true/false questions)
      remainingAnswers = [correctAnswer, { answer: incorrectAnswers[0], isGreyedOut: true }];
    }
  }

  onMount(async () => {
    isLoading = true;
    await fetchSessionToken(); // Retrieve a session token first
    await fetchQuestions(); // Fetch questions using the session token

    remainingAnswers = questions[currentQuestionIndex].answers;

    if (timerEnabled) {
      startTimer(); // Start the timer if enabled
    }
  });

  // Calculate the result message based on the percentage of correct answers
  function getResultMessage() {
    const correctAnswers = userAnswers.filter(answer => answer.isCorrect).length;
    const percentage = (correctAnswers / userAnswers.length) * 100;

    if (percentage === 100) {
      return `You’re a Wildlife Guru!`;
    } else if (percentage >= 80) {
      return `You’re a Wildlife Expert!`;
    } else if (percentage >= 60) {
      return `You’re a Wildlife Explorer!`;
    } else if (percentage >= 40) {
      return `You’re a Wildlife Adventurer!`;
    } else if (percentage >= 20) {
      return `You’re a Wildlife Wanderer!`;
    } else {
      return `You’re a Wildlife Newbie!`;
    }
  }

  // Get the fun tip based on the percentage of correct answers
  function getFunTip() {
    const correctAnswers = userAnswers.filter(answer => answer.isCorrect).length;
    const percentage = (correctAnswers / userAnswers.length) * 100;

    if (percentage === 100) {
      return "You’ve earned the crown of an explorer!";
    } else if (percentage >= 80) {
      return "Impressive! You’ve shown you have a deep understanding of wildlife.";
    } else if (percentage >= 60) {
      return "You’ve got a solid understanding of wildlife, but there’s still more to discover!";
    } else if (percentage >= 40) {
      return "Not bad! You’ve got the basics down, but there’s plenty more to uncover.";
    } else if (percentage >= 20) {
      return "You’re just starting your wildlife journey. There’s so much to learn, and you’re on the right track!";
    } else {
      return "Looks like you’ve just started your wildlife exploration! Don’t worry; every expert was once a beginner.";
    }
  }

  // Calculate the percentage of correct answers
  function getCorrectPercentage() {
    const correctAnswers = userAnswers.filter(answer => answer.isCorrect).length;
    const percentage = (correctAnswers / userAnswers.length) * 100;
    return Math.round(percentage);
  }
</script>

<div id="quiz-page">
  {#if isLoading}
    <p>Loading questions...</p>
  {:else if quizCompleted}
    <!-- Overview Section -->
    <div class="overview">
      <h2>{getResultMessage()}</h2>
      <p class="fun-tip">{getFunTip()}</p>
      <div class="progress-circle">
        <svg viewBox="0 0 36 36" class="circular-chart">
          <path class="circle-bg"
            d="M18 2.0845
              a 15.9155 15.9155 0 0 1 0 31.831
              a 15.9155 15.9155 0 0 1 0 -31.831"
          />
          <path class="circle"
            stroke-dasharray="{getCorrectPercentage()}, 100"
            d="M18 2.0845
              a 15.9155 15.9155 0 0 1 0 31.831
              a 15.9155 15.9155 0 0 1 0 -31.831"
          />
          <text x="18" y="20.35" class="percentage">{getCorrectPercentage()}%</text>
        </svg>
      </div>
      <table>
        <thead>
          <tr>
            <th>Number</th>
            <th>Question</th>
            <th>Your Answer</th>
            <th>Correct Answer</th>
          </tr>
        </thead>
        <tbody>
          {#each userAnswers as answer, index}
            <tr>
              <td>{index + 1}</td>
              <td>{@html answer.question}</td>
              <td class={answer.isCorrect ? "correct" : "incorrect"}>{@html answer.selectedAnswer}</td>
              <td>{@html answer.correctAnswer}</td>
            </tr>
          {/each}
        </tbody>
      </table>
      <button class="play-again-button" on:click={playAgain}>Play Again</button>
      <button class="new-game-button" on:click={newGame}>New Game</button>
    </div>
  {:else if questions.length > 0}
    <!-- Progress Bar Section -->
    <div class="progress-bar">
      {#each createArray(amount) as _, index}
        <div class="progress-line {userAnswers[index] ? (userAnswers[index].isCorrect ? 'correct' : userAnswers[index].selectedAnswer === 'You skipped' ? 'skipped' : 'incorrect') : ''}"></div>
      {/each}
    </div>
    <!-- Timer Section -->
    {#if timerEnabled}
      <div class="timer">
        <p>Time Remaining: {timer} seconds</p>
      </div>
    {/if}
    <!-- Question Section -->
    <div class="question-container">
      <h2 class="question">
        {@html questions[currentQuestionIndex].question}
      </h2>
      <div class="answers">
        {#each remainingAnswers as answer}
          <button
            class="answer-button"
            on:click={() => handleAnswerSelection(answer.answer || answer)}
            class:selected={selectedAnswer === (answer.answer || answer)}
            class:correct={selectedAnswer !== null && (answer.answer || answer) === questions[currentQuestionIndex].correct_answer}
            class:incorrect={selectedAnswer !== null && selectedAnswer === (answer.answer || answer) && (answer.answer || answer) !== questions[currentQuestionIndex].correct_answer}
            disabled={selectedAnswer !== null || answer.isGreyedOut}
            style={answer.isGreyedOut ? 'background-color: lightgrey;' : ''}
          >
            {@html answer.answer || answer}
          </button>
        {/each}
      </div>
      {#if selectedAnswer === null}
        <button class="joker-button" on:click={useJoker} disabled={jokerUsed}>
          50:50 Joker
        </button>
      {/if}
      {#if selectedAnswer !== null && currentQuestionIndex < questions.length - 1}
        <button class="next-button" on:click={() => nextQuestion(false)}>
          Next Question
        </button>
      {/if}
      {#if selectedAnswer !== null && currentQuestionIndex === questions.length - 1}
        <button class="next-button" on:click={() => (quizCompleted = true)}>
          Finish Quiz
        </button>
      {/if}
    </div>
  {:else}
    <p>No questions available. Please try again later.</p>
  {/if}
</div>

<style>
  #quiz-page {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 2rem;
  }

  .question-container, .overview {
    text-align: center;
    max-width: 600px;
    margin: auto;
  }

  .question, .overview h2 {
    font-size: 1.5rem;
    margin-bottom: 1rem;
  }

  .fun-tip {
    font-size: 0.9rem;
    color: #666;
    margin-bottom: 1rem;
  }

  .answers {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .answer-button {
    padding: 0.5rem 1rem;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    cursor: pointer;
    background-color: white;
    transition: background-color 0.3s ease;
  }

  .answer-button:hover:not([disabled]) {
    background-color: #f0f0f0;
  }

  .answer-button.selected {
    background-color: lightblue;
  }

  .answer-button.correct {
    background-color: lightgreen;
  }

  .answer-button.incorrect {
    background-color: lightcoral;
  }

  .answer-button[disabled] {
    cursor: not-allowed;
    opacity: 0.6;
  }

  .next-button, .play-again-button, .new-game-button, .joker-button {
    margin-top: 1rem;
    padding: 0.5rem 1rem;
    font-size: 1rem;
    border: none;
    border-radius: 4px;
    background-color: #007bff;
    color: white;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }

  .next-button:hover, .play-again-button:hover, .new-game-button:hover, .joker-button:hover {
    background-color: #0056b3;
  }

  .joker-button[disabled] {
    background-color: grey;
    cursor: not-allowed;
  }

  .correct {
    color: green;
    font-weight: bold;
  }

  .incorrect {
    color: red;
    font-weight: bold;
  }

  .progress-bar {
    display: flex;
    gap: 0.5rem;
    margin-bottom: 1rem;
    width: 100%;
  }

  .progress-line {
    flex: 1;
    height: 5px;
    background-color: grey;
    border-radius: 4px;
  }

  .progress-line.correct {
    background-color: lightgreen;
  }

  .progress-line.incorrect {
    background-color: lightcoral;
  }

  .progress-line.skipped {
    background-color: darkgrey;
  }

  .timer {
    margin-bottom: 1rem;
    font-size: 1.2rem;
    font-weight: bold;
  }

  .progress-circle {
    margin: 1rem 0;
  }

  .circular-chart {
    display: block;
    margin: 10px auto;
    max-width: 80%;
    max-height: 250px;
  }

  .circle-bg {
    fill: none;
    stroke: #eee;
    stroke-width: 3.8;
  }

  .circle {
    fill: none;
    stroke-width: 2.8;
    stroke-linecap: round;
    animation: progress 1s ease-out forwards;
  }

  .circle {
    stroke: #4caf50;
  }

  .percentage {
    fill: #666;
    font-size: 0.5em;
    text-anchor: middle;
  }

  @keyframes progress {
    0% {
      stroke-dasharray: 0 100;
    }
  }

  table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 1rem;
  }

  th, td {
    border: 1px solid #1e1e1e;
    padding: 0.5rem;
    text-align: left;
  }

  th {
    background-color: var(--website-dark-green-color);
  }

  td.correct {
    color: green;
  }

  td.incorrect {
    color: red;
  }
</style>