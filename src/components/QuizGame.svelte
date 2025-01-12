<script>
  import { onMount } from "svelte";

  export let mode = "easy"; // Default to easy mode if no mode is passed
  let questions = [];
  let currentQuestionIndex = 0;
  let selectedAnswer = null;
  let isCorrect = null;

  // Fetch questions based on the selected mode
  async function fetchQuestions() {
    try {
      const response = await fetch(
        `https://opentdb.com/api.php?amount=10&category=27&difficulty=${mode}`
      );
      const data = await response.json();
      questions = data.results.map((q) => ({
        ...q,
        answers: shuffleArray([...q.incorrect_answers, q.correct_answer]),
      }));
    } catch (error) {
      console.error("Failed to fetch questions:", error);
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

  onMount(() => {
    fetchQuestions();
  });
</script>

<div id="quiz-page">
  {#if questions.length > 0}
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
            class:correct={isCorrect && answer === questions[currentQuestionIndex].correct_answer}
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
    <p>Loading questions...</p>
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
