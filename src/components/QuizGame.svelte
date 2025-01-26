<script>
  import { createEventDispatcher, onMount } from "svelte";
  import QuizOverview from "./QuizOverview.svelte";
  import animalData from "../assets/animaldata.js";

  export let mode = "easy"; // Default to easy mode if no mode is passed
  export let amount = 8; // Default question amount if no amount is passed
  export let type = "any"; // Default question type if no type is passed
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

  // Fetch questions based on the selected mode, amount, type, and session token
  async function fetchQuestions(retryCount = 0) {
    if (type === "comparison") {
      generateComparisonQuestions();
      isLoading = false;
      return;
    }

    if (!sessionToken) {
      console.error("Session token is missing. Unable to fetch questions.");
      return;
    }

    try {
      const typeParam = type === "any" ? "" : `&type=${type}`;
      const response = await fetch(
        `https://opentdb.com/api.php?amount=${amount}&category=27&difficulty=${mode}${typeParam}&token=${sessionToken}`
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

  // Generate comparison questions
  function generateComparisonQuestions() {
    const attributes = [
      "top_speed",
      "max_weight",
      "max_length",
      "intelligence",
      "max_age",
      "deaths",
      "litter_size",
    ];

    for (let i = 0; i < amount; i++) {
      const comparisonAttribute = attributes[Math.floor(Math.random() * attributes.length)];
      const currentAnimals = getRandomAnimals();
      const question = `Which one has ${
        comparisonAttribute === "top_speed"
          ? "higher top speed"
          : comparisonAttribute === "max_weight"
          ? "greater weight"
          : comparisonAttribute === "max_length"
          ? "greater length"
          : comparisonAttribute === "intelligence"
          ? "higher intelligence"
          : comparisonAttribute === "max_age"
          ? "longer lifespan"
          : comparisonAttribute === "deaths"
          ? "more deaths caused"
          : "larger litter size"
      }?`;
      const correctAnimal = currentAnimals.reduce((prev, current) =>
        current[comparisonAttribute] > prev[comparisonAttribute] ? current : prev
      );

      questions.push({
        question,
        answers: currentAnimals,
        correct_answer: correctAnimal,
        comparisonAttribute,
      });
    }
  }

  // Get random animals based on the selected mode
  function getRandomAnimals() {
    const numAnimals = mode === "easy" ? 2 : mode === "medium" ? 3 : 4;
    const shuffledAnimals = shuffleArray(animalData);
    return shuffledAnimals.slice(0, numAnimals);
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

    if (type === "comparison") {
      // For comparison questions, mark two wrong options as grey and move them to the bottom
      if (incorrectAnswers.length > 1) {
        remainingAnswers = [correctAnswer, incorrectAnswers[0], ...incorrectAnswers.slice(1).map(answer => ({ ...answer, isGreyedOut: true }))];
      } else {
        remainingAnswers = [correctAnswer, { ...incorrectAnswers[0], isGreyedOut: true }];
      }
    } else {
      // For other types of questions, mark two wrong options as grey
      if (incorrectAnswers.length > 1) {
        remainingAnswers = [correctAnswer, incorrectAnswers[0], ...incorrectAnswers.slice(1).map(answer => ({ answer, isGreyedOut: true }))];
      } else {
        remainingAnswers = [correctAnswer, { answer: incorrectAnswers[0], isGreyedOut: true }];
      }
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
</script>

<div id="quiz-page">
  {#if isLoading}
    <p>Loading questions...</p>
  {:else if quizCompleted}
    <!-- Overview Section -->
    <QuizOverview {userAnswers} {playAgain} {newGame} />
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
      {#if type === "comparison"}
        <div class="answers">
          {#each remainingAnswers as animal}
            <button
              class="answer-button"
              on:click={() => handleAnswerSelection(animal)}
              class:selected={selectedAnswer === animal}
              class:correct={selectedAnswer !== null && animal === questions[currentQuestionIndex].correct_answer}
              class:incorrect={selectedAnswer !== null && selectedAnswer === animal && animal !== questions[currentQuestionIndex].correct_answer}
              disabled={selectedAnswer !== null || animal.isGreyedOut}
              style={animal.isGreyedOut ? 'background-color: lightgrey;' : ''}
            >
              <img src={`/images/${animal.group.toLowerCase()}${animal.group_number}.png`} alt={animal.name} />
              <p>{animal.name}</p>
            </button>
          {/each}
        </div>
      {:else}
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
      {/if}
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

  .question-container {
    text-align: center;
    max-width: 600px;
    margin: auto;
  }

  .question{
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

  .answer-button img {
    width: 100px;
    height: 100px;
    object-fit: cover;
    border-radius: 8px;
    margin-bottom: 0.5rem;
  }

  .answer-button p {
    margin: 0;
    font-size: 1rem;
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

  .next-button, .joker-button {
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

  .next-button:hover, .joker-button:hover {
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

  @keyframes progress {
    0% {
      stroke-dasharray: 0 100;
    }
  }
</style>