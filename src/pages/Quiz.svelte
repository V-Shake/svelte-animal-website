<script>
  import QuizGame from "../components/QuizGame.svelte";
  import { IconPawFilled, IconSwords, IconUser, IconLock } from "@tabler/icons-svelte";

  let selectedMode = "easy"; // Default mode for Quiz
  let questionAmount = "8"; // Default question amount for Quiz
  let quizStarted = false;
  let timerEnabled = false; // Timer toggle state for Quiz
  let questionType = "any"; // Default question type for Quiz

  function handleStart() {
    quizStarted = true; // Start the quiz
  }

  function handleNewGame() {
    quizStarted = false; // Reset to go back to the selection screen
  }
</script>

<main class="main-container">
  {#if !quizStarted}
    <!-- Placeholder for the left container -->
    <div class="left-container">
      <div class="rectangle">
        <div class="icon-lock-container">
          <IconLock class="icon-lock" stroke={1} size={48} />
        </div>
        <div class="header">Multiplayer Mode</div>
        <div style="display: flex; justify-content: space-between; width: 100%; padding: 40px 30px; color: var(--dark-color);">
          <IconUser stroke={1} size={60} />
          <IconSwords stroke={1} size={60} />
          <IconUser stroke={1} size={60} />
        </div>
        <div class="subtitle">play with other players</div>
        <div class="rectangle-thick-line"></div>
        <div class="rectangle-description">random match or friend match</div>
      </div>
    </div>

    <!-- Centered container for description -->
    <div class="centered-container">
      <div class="circle-background"></div>
      <div class="border-circle-background"></div>
      <div class="icon-paw-container">
        <IconPawFilled stroke={1} size={120} />
      </div>
      <div class="description-container">
        <div class="description">
          <p>Welcome to the Quiz Game!</p>
        </div>
        <div class="thick-line"></div>
        <div class="additional-text">
          <p>
            Ready to test your wildlife knowledge? Pick a difficulty—Easy, Medium,
            or Hard—and explore the animal kingdom with fun facts and surprises.
            Use the Joker to get hints if you’re stuck! Whether you're a beginner
            or an expert, there's something for everyone!
          </p>
        </div>
      </div>
    </div>

    <!-- Right container for quiz selection -->
    <div class="right-container">
      <div class="selection-container">
        <h2>Select Quiz Settings</h2>
        <label for="mode">Mode:</label>
        <select id="mode" bind:value={selectedMode}>
          <option value="easy">Easy</option>
          <option value="medium">Medium</option>
          <option value="hard">Hard</option>
        </select>

        <label for="amount">Question Amount:</label>
        <select id="amount" bind:value={questionAmount}>
          <option value="8">8</option>
          <option value="12">12</option>
          <option value="16">16</option>
        </select>

        <label for="type">Question Type:</label>
        <select id="type" bind:value={questionType}>
          <option value="any">Any Type</option>
          <option value="multiple">Multiple Choice</option>
          <option value="boolean">True/False</option>
          <option value="comparison">Comparison</option>
        </select>

        <label for="timer">Enable Timer:</label>
        <input type="checkbox" id="timer" bind:checked={timerEnabled} />

        <button class="start-button" on:click={handleStart}>Start Quiz</button>
      </div>
    </div>
  {/if}

  {#if quizStarted}
    <div class="quiz-game-container">
      <QuizGame
        mode={selectedMode}
        amount={questionAmount}
        type={questionType}
        {timerEnabled}
        on:newGame={handleNewGame}
      />
    </div>
  {/if}
</main>

<style>
  .main-container {
    display: flex;
    justify-content: space-between; /* Distribute the containers evenly */
    align-items: center; /* Align vertically in the center */
    height: 90vh; /* Full height */
    padding: 0 8rem; /* Add padding on both sides */
  }

  .left-container {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .rectangle {
    position: relative;
    flex-grow: 0;
    min-width: 320px;
    min-height: 470px;
    background-color: var(--website-dark-green-color);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 1rem;
    box-sizing: border-box;
    border-radius: 15px;
    transition: all 0.3s ease;
    color: var(--dark-color); /* Set the font color to var(--dark-color) */
  }

  .icon-lock-container {
    position: absolute;
    top: 1.5rem;
    left: 50%;
    transform: translateX(-50%);
    color: var(--card-background-color);
  }

  .rectangle:hover .icon-lock-container {
    color: var(--website-green-color);
  }

  .header {
    font-size: 24px;
    font-weight: bold;
    margin-bottom: 10px;
  }

  .subtitle {
    font-size: 18px;
    margin-bottom: 20px;
  }

  .rectangle-thick-line {
    width: 100%;
    height: var(--thick-line-strength);
    background-color: var(--dark-color);
    margin-bottom: 20px;
  }

  .rectangle-description {
    font-size: 0.9rem;
    text-align: center;
  }

  .description {
    font-size: 14px;
    text-align: center;
  }

  .centered-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    position: relative;
    flex: 1;
    text-align: center;
  }

  .circle-background {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 300px;
    height: 300px;
    background-color: var(--website-dark-green-color);
    border-radius: 50%;
    z-index: -10;
    backdrop-filter: blur(5px);
  }

  .border-circle-background {
    position: absolute;
    top: 15%;
    left: 45%;
    transform: translate(-50%, -50%);
    width: 120px;
    height: 120px;
    border: 1px solid var(--website-green-color);
    opacity: 0.2;
    border-radius: 50%;
    z-index: -11;
  }

  .icon-paw-container {
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 50%;
    padding: 1rem;
    color: var(--website-green-color);
  }

  .description-container {
    width: 25rem;
    height: auto;
    padding: 1rem;
    position: relative;
    z-index: 1;
    box-shadow: 0 4px 30px rgba(3, 3, 3, 0.1);
    backdrop-filter: blur(6.9px);
    -webkit-backdrop-filter: blur(6.9px);
    border: 1px solid rgba(27, 27, 27, 0.52);
    border-radius: 15px;
  }

  .description {
    font-size: 1.2rem;
    font-weight: bold;
  }

  .additional-text {
    font-size: 0.8rem;
    padding: 0 1rem;
  }

  .thick-line {
    width: 90%;
    height: var(--thick-line-strength);
    background-color: rgba(255, 255, 255, 0.4);
    margin: 1rem auto;
  }

  .right-container {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
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

  select,
  input[type="checkbox"] {
    width: 100%;
    padding: 0.5rem;
    margin-top: 0.5rem;
  }

  .start-button {
    margin-top: 2rem;
    padding: 0.5rem 1rem;
    font-size: 1rem;
    border: none;
    background-color: #007bff;
    color: white;
    cursor: pointer;
    border-radius: 4px;
  }

  .start-button:hover {
    background-color: #0056b3;
  }

  .quiz-game-container {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
  }
</style>