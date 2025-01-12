<script>
  import StartButton from "../components/StartButton.svelte";
  import QuizGame from "../components/QuizGame.svelte";

  let selectedMode = null;

  function handleStart(mode) {
    selectedMode = mode; // Set the mode to start the quiz
  }
</script>

{#if !selectedMode}
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
{:else}
  <QuizGame mode={selectedMode} />
{/if}

<style>
  #quiz-page {
    display: flex;
    justify-content: space-around;
    align-items: center;
    padding: 2rem;
  }
  .mode-container {
    border: 2px solid #ccc;
    border-radius: 8px;
    padding: 1.5rem;
    text-align: center;
    width: 20%;
  }
</style>
