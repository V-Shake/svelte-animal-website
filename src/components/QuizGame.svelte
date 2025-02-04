<script>
  import { createEventDispatcher, onMount } from "svelte";
  import QuizOverview from "./QuizOverview.svelte";
  import animalData from "../assets/animaldata.js";
  import { IconLogout2 } from "@tabler/icons-svelte";

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
  let timer = 15; // Timer countdown in seconds
  let timerInterval;
  let jokerUsed = false; // Track if the joker has been used
  let remainingAnswers = []; // Track the remaining answers after using the joker

  const dispatch = createEventDispatcher();

  // Fetch a new session token
  async function fetchSessionToken() {
    try {
      const response = await fetch(
        "https://opentdb.com/api_token.php?command=request",
      );
      const data = await response.json();
      sessionToken = data.token;
      console.log("Session token fetched:", sessionToken);
    } catch (error) {
      console.error("Failed to fetch session token:", error);
    }
  }

  // Reset the session token
  async function resetSessionToken() {
    try {
      const response = await fetch(
        `https://opentdb.com/api_token.php?command=reset&token=${sessionToken}`,
      );
      const data = await response.json();
      console.log("Session token reset:", data);
    } catch (error) {
      console.error("Failed to reset session token:", error);
    }
  }

  // Fetch questions based on the selected mode, amount, type, and session token
  async function fetchQuestions(retryCount = 0) {
    if (!sessionToken) {
      console.error("Session token is missing. Unable to fetch questions.");
      return;
    }

    try {
      const typeParam = type === "any" ? "" : `&type=${type}`;
      const response = await fetch(
        `https://opentdb.com/api.php?amount=${amount}&category=27&difficulty=${mode}${typeParam}&token=${sessionToken}`,
      );

      if (response.status === 429) {
        if (retryCount < 5) {
          console.log(
            `Rate limit exceeded. Retrying in ${retryCount + 1} seconds...`,
          );
          await new Promise((resolve) =>
            setTimeout(resolve, (retryCount + 1) * 1000),
          );
          await fetchQuestions(retryCount + 1);
        } else {
          console.error("Rate limit exceeded. Please try again later.");
        }
        return;
      }

      const data = await response.json();
      console.log("Fetched data:", data);

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
        console.log("Questions fetched:", questions);
      } else {
        console.error("No questions available.");
      }

      if (type === "any") {
        // Determine the number of comparison questions based on the total amount
        let comparisonCount = Math.floor(amount / 4);
        if (amount >= 16) {
          comparisonCount = Math.floor(amount / 2);
        }

        // Generate the comparison questions
        const comparisonQuestions =
          generateComparisonQuestions(comparisonCount);

        // Ensure the total amount is correct
        const remainingQuestions = amount - comparisonQuestions.length;

        // Replace some API questions with the comparison questions
        questions = [
          ...questions.slice(0, remainingQuestions),
          ...comparisonQuestions,
        ];

        // Shuffle to mix comparison and API questions
        questions = shuffleArray(questions);
      } else if (type === "comparison") {
        // Generate only comparison questions
        questions = generateComparisonQuestions(amount);
      }

      isLoading = false;
      remainingAnswers = questions[currentQuestionIndex].answers;
      console.log("Remaining answers:", remainingAnswers);
    } catch (error) {
      console.error("Failed to fetch questions:", error);
    }
  }

  // Generate comparison questions
  function generateComparisonQuestions(count) {
    const attributes = [
      "top_speed",
      "max_weight",
      "max_length",
      "intelligence",
      "max_age",
      "deaths",
      "litter_size",
    ];

    const comparisonQuestions = [];
    for (let i = 0; i < count; i++) {
      const comparisonAttribute =
        attributes[Math.floor(Math.random() * attributes.length)];
      const currentAnimals = getRandomAnimals();

      // Ensure animals have required properties
      const validAnimals = currentAnimals.filter(
        (animal) => animal.group && animal.group_number && animal.name,
      );

      if (validAnimals.length < currentAnimals.length) {
        console.warn(
          "Some animals are missing image properties and were excluded.",
        );
      }

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

      const correctAnimal = validAnimals.reduce((prev, current) =>
        current[comparisonAttribute] > prev[comparisonAttribute]
          ? current
          : prev,
      );

      comparisonQuestions.push({
        question,
        answers: validAnimals,
        correct_answer: correctAnimal,
        comparisonAttribute,
      });
    }
    return comparisonQuestions;
  }

  // Get random animals based on the selected mode
  function getRandomAnimals() {
    const numAnimals = mode === "easy" ? 2 : mode === "medium" ? 3 : 4;
    const shuffledAnimals = shuffleArray(animalData);
    return shuffledAnimals.slice(0, numAnimals);
  }

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
    timer = 15;
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
    const incorrectAnswers = questions[currentQuestionIndex].answers.filter(
      (answer) => answer !== correctAnswer,
    );

    if (questions[currentQuestionIndex].comparisonAttribute) {
      // For comparison questions, mark two wrong options as grey and move them to the bottom
      if (incorrectAnswers.length > 1) {
        remainingAnswers = [
          correctAnswer,
          incorrectAnswers[0],
          ...incorrectAnswers.slice(1).map((answer) => ({
            ...answer,
            isGreyedOut: true,
            crossedOut: true,
          })),
        ];
      } else {
        remainingAnswers = [
          correctAnswer,
          { ...incorrectAnswers[0], isGreyedOut: true, crossedOut: true },
        ];
      }
    } else {
      // For other types of questions, mark two wrong options as grey
      if (incorrectAnswers.length > 1) {
        remainingAnswers = [
          correctAnswer,
          incorrectAnswers[0],
          ...incorrectAnswers
            .slice(1)
            .map((answer) => ({ answer, isGreyedOut: true, crossedOut: true })),
        ];
      } else {
        remainingAnswers = [
          correctAnswer,
          { ...incorrectAnswers[0], isGreyedOut: true, crossedOut: true },
        ];
      }
    }

    // Ensure the answers are displayed correctly
    remainingAnswers = remainingAnswers.map((answer) =>
      typeof answer === "object" ? answer : { answer },
    );
  }

  $: console.log("Remaining Answers:", remainingAnswers);

  onMount(async () => {
    isLoading = true;
    await fetchSessionToken(); // Retrieve a session token first
    await fetchQuestions(); // Fetch questions using the session token

    if (questions.length > 0) {
      remainingAnswers = questions[currentQuestionIndex].answers;
      isLoading = false;
    } else {
      console.error("No questions fetched.");
      isLoading = false;
    }

    if (timerEnabled) {
      startTimer(); // Start the timer if enabled
    }
  });
</script>

{#if !quizCompleted}
  <div class="quit-button" on:click={newGame}>
    <IconLogout2 stroke={1} size={24} />
    <span>quit</span>
  </div>
{/if}

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
        <div
          class="progress-line {userAnswers[index]
            ? userAnswers[index].isCorrect
              ? 'correct'
              : userAnswers[index].selectedAnswer === 'You skipped'
                ? 'skipped'
                : 'incorrect'
            : ''}"
        ></div>
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
      {#if questions[currentQuestionIndex].comparisonAttribute}
      <div class="answer-buttons-container">
        {#each remainingAnswers as animal}
          <button
            class="answer-button"
            on:click={() => handleAnswerSelection(animal)}
            class:selected={selectedAnswer === animal}
            class:correct={selectedAnswer !== null && animal === questions[currentQuestionIndex].correct_answer}
            class:incorrect={selectedAnswer !== null && selectedAnswer === animal && animal !== questions[currentQuestionIndex].correct_answer}
            class:crossedOut={animal.isGreyedOut}
            class:isGreyedOut={animal.isGreyedOut}
            disabled={selectedAnswer !== null || animal.isGreyedOut}
          >
            <div class="answer-content">
              <img
                src={`/images/${animal.group.toLowerCase()}${animal.group_number}.png`}
                alt={animal.name}
              />
              <div class="text-group">
                <span class="radio-circle"></span>
                <p>{animal.name}</p>
              </div>
            </div>
          </button>
        {/each}
      </div>
      
      {:else}
        <div class="answers">
          {#each remainingAnswers as answer}
            <button
              class="answer-button
              {answer.crossedOut ? 'crossedOut' : ''}
              {answer.isGreyedOut ? 'isGreyedOut' : ''}"
              on:click={() => handleAnswerSelection(answer.answer || answer)}
              class:selected={selectedAnswer === (answer.answer || answer)}
              class:correct={selectedAnswer !== null &&
                (answer.answer || answer) ===
                  questions[currentQuestionIndex].correct_answer}
              class:incorrect={selectedAnswer !== null &&
                selectedAnswer === (answer.answer || answer) &&
                (answer.answer || answer) !==
                  questions[currentQuestionIndex].correct_answer}
              disabled={selectedAnswer !== null || answer.isGreyedOut}
              style={answer.isGreyedOut ? "" : ""}
            >
              <span class="radio-circle"></span>
              <!-- Circle -->
              <span class="answer-text">{@html answer.answer || answer}</span>
              <!-- Answer Text -->
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
    max-width: 35rem;
    margin: auto;
  }

  .question {
    font-size: 1.5rem;
    margin-bottom: 3rem;
  }

  .answers {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    align-items: center;
    justify-content: center;
  }

  .answer-buttons-container {
    display: flex; /* Use flexbox */
    justify-content: center; /* Center the buttons horizontally */
    align-items: center; /* Center the buttons vertically if needed */
    gap: 1rem;
  }

  .answer-buttons-container .answer-button {
    width: 20rem;
    height: 20rem;
    display: flex;
    align-items: center; /* Vertically centers the text */
    justify-content: center; /* Horizontally centers the text */
    position: relative; /* For absolute positioning of the radio button */
    text-align: center;
  }
  
  .text-group {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    gap: 2rem;
    margin-left: -1rem;
  }
 
  .answer-buttons-container .answer-content {
    display: flex;
    flex-direction: column; /* Stack the radio circle and text vertically */
    align-items: center; /* Center horizontally */
    justify-content: center; /* Center vertically */
  }

  .answer-button {
    display: flex;
    flex-direction: row; /* Stack content vertically inside the button */
    justify-content: center;
    align-items: center;
    padding: 1rem;
    font-size: 1rem;
    cursor: pointer;
    background-color: var(--grey-color);
    color: white;
    border: none;
    outline: none;
    border-radius: 10px;
    transition: all 0.3s ease;
    opacity: 1;
    width: 30rem;
    text-align: center;
  }

  .answer-button img {
    width: 230px;
    height: 230px;
    border-radius: 10px;
    margin-bottom: 1rem;
  }

  .radio-circle {
    width: 14px;
    height: 14px;
    border: 2px solid white;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background-color 0.3s ease;
  }

  .answer-button.selected .radio-circle {
    background-color: white; /* Filled when selected */
  }

  .answer-text {
    flex-grow: 1; /* Take up available space */
    text-align: center; /* Center text */
  }

  .answer-content {
    display: flex;
    flex-direction: column; /* Stack the radio circle and text vertically */
    align-items: center; /* Center horizontally */
    justify-content: center; /* Center vertically */
  }

  .answer-button .radio-circle {
    margin-right: 4px; /* Spacing between radio circle and text */
  }

  .answer-button p {
    margin: 0;
    font-size: 1rem;
  }

  .answer-button:hover:not([disabled]) {
    background-color: rgb(49, 49, 49);
  }

  .answer-button.selected {
    background-color: rgb(83, 86, 87);
    color: white;
  }

  .answer-button.correct {
    background-color: var(--quiz-correct-color);
    color: white;
  }

  .answer-button.incorrect {
    background-color: var(--quiz-wrong-color);
    color: white;
  }

  .answer-button[disabled] {
    cursor: not-allowed;
  }

  button:disabled {
    background-color: rgb(20, 20, 20);
    color: rgb(83, 86, 87);
  }

  .next-button,
  .joker-button {
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

  .next-button:hover,
  .joker-button:hover {
    background-color: #0056b3;
  }

  .joker-button[disabled] {
    background-color: grey;
    cursor: not-allowed;
  }

  .correct {
    color: white;
  }

  .incorrect {
    color: white;
  }

  .answer-button.crossedOut {
    text-decoration: line-through;
    color: var(--quiz-wrong-color);
  }

  .answer-button.isGreyedOut {
    background-color: rgb(20, 20, 20);
  }

  .progress-bar {
    position: absolute;
    top: 3.6rem;
    padding: 0 16rem;
    display: flex;
    gap: 0.5rem;
    margin-bottom: 1rem;
    width: 100%;
  }

  .progress-line {
    flex: 1;
    height: 5px;
    background-color: var(--grey-color);
    border-radius: 4px;
  }

  .progress-line.correct {
    background-color: var(--quiz-correct-color);
  }

  .progress-line.incorrect {
    background-color: var(--quiz-wrong-color);
  }

  .progress-line.skipped {
    background-color: var(--dark-color);
  }

  .timer {
    margin-bottom: 1rem;
    font-size: 1.2rem;
    font-weight: bold;
  }

  .quit-button {
    position: absolute;
    top: 3rem;
    left: 6.5rem;
    display: flex;
    align-items: center;
    cursor: pointer;
    color: var(--card-background-color);
    transition: all 0.3s ease;
    text-decoration: none;
    font-size: 0.8rem;
  }

  .quit-button:hover {
    color: var(--website-green-color); /* Change background color on hover */
  }

  .quit-button span {
    margin-left: 5px;
  }
</style>
