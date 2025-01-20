<script>
  import QuizGame from "../components/QuizGame.svelte";
  import FaceOff from "../components/FaceOff.svelte";

  let selectedMode = "easy"; // Default mode
  let questionAmount = "5"; // Default question amount
  let quizStarted = false;
  let faceOffStarted = false;
  let timerEnabled = false; // Timer toggle state

  function handleStart() {
    quizStarted = true; // Start the quiz
  }

  function handlePlayAgain() {
    quizStarted = false; // Reset to go back to the selection screen
  }

  function handleFaceOffStart() {
    faceOffStarted = true; // Start the Animal Face-Off
  }

  function handleFaceOffPlayAgain() {
    faceOffStarted = false; // Reset to go back to the selection screen
  }
</script>

{#if !quizStarted && !faceOffStarted}
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
          <option value="5">5</option>
          <option value="10">10</option>
          <option value="15">15</option>
        </select>

        <label for="timer">Enable Timer:</label>
        <input type="checkbox" id="timer" bind:checked={timerEnabled} />

        <button class="start-button" on:click={handleStart}>Start Quiz</button>
      </div>
      <div class="faceoff-container">
        <h2>Animal Face-Off</h2>
        <button class="start-button" on:click={handleFaceOffStart}>Start Face-Off</button>
      </div>
    </div>
  </main>
{:else if quizStarted}
  <QuizGame mode={selectedMode} amount={questionAmount} timerEnabled={timerEnabled} on:playAgain={handlePlayAgain} />
{:else if faceOffStarted}
  <FaceOff on:playAgain={handleFaceOffPlayAgain} />
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
    justify-content: space-between;
    align-items: flex-start;
    padding: 1rem;
    width: 100%;
    max-width: 1200px;
  }

  .selection-container, .faceoff-container {
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

  select, input[type="checkbox"] {
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