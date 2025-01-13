<script>
  import StartButton from "../components/StartButton.svelte";
  import QuizGame from "../components/QuizGame.svelte";

  let selectedMode = null;

  function handleStart(mode) {
    selectedMode = mode; // Set the mode to start the quiz
  }
</script>

{#if !selectedMode}
  <main class="main-container">
    <div id="quiz-page">
      <div class="mode-container">
        <h2>Easy Mode</h2>
        <p>Perfect for beginners!</p>
        <StartButton on:start={() => handleStart("easy")} />
      </div>

      <div class="mode-container">
        <h2>Medium Mode</h2>
        <p>Challenge yourself with tougher questions.</p>
        <StartButton on:start={() => handleStart("medium")} />
      </div>

      <div class="mode-container">
        <h2>Hard Mode</h2>
        <p>Ready for the ultimate test?</p>
        <StartButton on:start={() => handleStart("hard")} />
      </div>
    </div>
  </main>
{:else}
  <QuizGame mode={selectedMode} />
{/if}

<style>
  .main-container {
    display: flex;
    justify-content: space-around;
    align-items: center; /* Align vertically in the center */
    height: 90vh; /* Full height */
  }

  #quiz-page {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem;
    gap: 8rem;
  }
  .mode-container {
    border: 2px solid #ccc;
    border-radius: 8px;
    padding: 1rem;
    text-align: center;
    width: 20rem;
    height: 15rem;
  }
</style>
