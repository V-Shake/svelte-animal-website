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

      // Reset timer for the next question
      if (timerEnabled) {
        startTimer();
      }
    } else {
      quizCompleted = true; // Show the overview
      clearInterval(timerInterval); // Stop the timer
    }
  }

  // Restart the quiz
  function playAgain() {
    dispatch("playAgain"); // Emit the playAgain event
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

  onMount(async () => {
    isLoading = true;
    await fetchSessionToken(); // Retrieve a session token first
    await fetchQuestions(); // Fetch questions using the session token

    if (timerEnabled) {
      startTimer(); // Start the timer if enabled
    }
  });
</script>

<div id="quiz-page">
  {#if isLoading}
    <p>Loading questions...</p>
  {:else if quizCompleted}
    <!-- Overview Section -->
    <div class="overview">
      <h2>Quiz Overview</h2>
      <ul>
        {#each userAnswers as answer, index}
          <li>
            <p><strong>Question {index + 1}:</strong> {@html answer.question}</p>
            <p>Your Answer: {@html answer.selectedAnswer}</p>
            <p>
              Correct Answer: 
              <span class={answer.isCorrect ? "correct" : "incorrect"}>
                {@html answer.correctAnswer}
              </span>
            </p>
          </li>
        {/each}
      </ul>
      <button class="play-again-button" on:click={playAgain}>Play Again</button>
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
        {#each questions[currentQuestionIndex].answers as answer}
          <button
            class="answer-button"
            on:click={() => handleAnswerSelection(answer)}
            class:selected={selectedAnswer === answer}
            class:correct={selectedAnswer !== null && answer === questions[currentQuestionIndex].correct_answer}
            class:incorrect={selectedAnswer !== null && selectedAnswer === answer && answer !== questions[currentQuestionIndex].correct_answer}
            disabled={selectedAnswer !== null}
          >
            {@html answer}
          </button>
        {/each}
      </div>
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

  .next-button, .play-again-button {
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

  .next-button:hover, .play-again-button:hover {
    background-color: #0056b3;
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
</style>