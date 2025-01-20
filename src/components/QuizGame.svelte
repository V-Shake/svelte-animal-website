<script>
  import { onMount } from "svelte";

  export let mode = "easy"; // Default to easy mode if no mode is passed
  let questions = [];
  let currentQuestionIndex = 0;
  let selectedAnswer = null;
  let isCorrect = null;
  let sessionToken = null; // Store the session token
  let isLoading = true; // Show loading state while fetching questions

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

  // Fetch questions based on the selected mode and session token
  async function fetchQuestions() {
    if (!sessionToken) {
      console.error("Session token is missing. Unable to fetch questions.");
      return;
    }

    try {
      const response = await fetch(
        `https://opentdb.com/api.php?amount=10&category=27&difficulty=${mode}&token=${sessionToken}`
      );
      const data = await response.json();

      if (data.response_code === 4) {
        // Token exhausted, reset it
        console.log("Exhausted all questions. Resetting session token.");
        await resetSessionToken();
        await fetchQuestions(); // Retry after resetting the token
        return;
      }

      questions = data.results.map((q) => ({
        ...q,
        answers: shuffleArray([...q.incorrect_answers, q.correct_answer]),
      }));
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
  }

  // Move to the next question
  function nextQuestion() {
    if (currentQuestionIndex < questions.length - 1) {
      currentQuestionIndex++;
      selectedAnswer = null;
      isCorrect = null;
    }
  }

  onMount(async () => {
    isLoading = true;
    await fetchSessionToken(); // Retrieve a session token first
    await fetchQuestions(); // Fetch questions using the session token
  });
</script>

<div id="quiz-page">
  {#if isLoading}
    <p>Loading questions...</p>
  {:else if questions.length > 0}
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
            class:correct={isCorrect === false && answer === questions[currentQuestionIndex].correct_answer || isCorrect && answer === questions[currentQuestionIndex].correct_answer}
            class:incorrect={!isCorrect && selectedAnswer === answer && answer !== questions[currentQuestionIndex].correct_answer}
            disabled={selectedAnswer !== null}
          >
            {@html answer}
          </button>
        {/each}
      </div>
      {#if selectedAnswer !== null}
        <button class="next-button" on:click={nextQuestion}>
          Next Question
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

  .question {
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

  .next-button {
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

  .next-button:hover {
    background-color: #0056b3;
  }
</style>
