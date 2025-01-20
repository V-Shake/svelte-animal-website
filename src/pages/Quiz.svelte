<script>
  import QuizGame from "../components/QuizGame.svelte";

  let selectedMode = "easy"; // Default mode
  let questionAmount = 10; // Default question amount
  let quizStarted = false;

  function handleStart() {
    quizStarted = true; // Start the quiz
  }

  function handlePlayAgain() {
    quizStarted = false; // Reset to go back to the selection screen
  }
</script>

{#if !quizStarted}
  <main class="main-container">
    <div id="quiz-page">
      <div class="selection-container">
        <h2>Select Quiz Mode and Question Amount</h2>
        <label for="mode">Mode:</label>
        <select id="mode" bind:value={selectedMode}>
          <option value="easy">Easy</option>
          <option value="medium">Medium</option>
          <option value="hard">Hard</option>
        </select>

        <label for="amount">Question Amount:</label>
        <select id="amount" bind:value={questionAmount}>
          <option value="10">10</option>
          <option value="15">15</option>
          <option value="20">20</option>
          <option value="25">25</option>
        </select>

        <button class="start-button" on:click={handleStart}>Start Quiz</button>
      </div>
    </div>
  </main>
{:else}
  <QuizGame mode={selectedMode} amount={questionAmount} on:playAgain={handlePlayAgain} />
{/if}

<style>
  .main-container {
    display: flex;
    justify-content: center;
    align-items: center; /* Align vertically in the center */
    height: 90vh; /* Full height */
  }

  #quiz-page {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 1rem;
  }

  .selection-container {
    text-align: center;
    border: 2px solid #ccc;
    border-radius: 8px;
    padding: 2rem;
    width: 20rem;
  }

  label {
    display: block;
    margin-top: 1rem;
  }

  select {
    width: 100%;
    padding: 0.5rem;
    margin-top: 0.5rem;
  }

  .start-button {
    margin-top: 2rem;
    padding: 0.5rem 1rem;
    font-size: 1rem;
    border: none;
    border-radius: 4px;
    background-color: #007bff;
    color: white;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }

  .start-button:hover {
    background-color: #0056b3;
  }
</style>