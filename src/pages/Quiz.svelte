<script>
  import QuizGame from "../components/QuizGame.svelte";
  import {
    IconPawFilled,
    IconSwords,
    IconUser,
    IconLock,
    IconArrowNarrowLeft,
  } from "@tabler/icons-svelte";

  let selectedMode = "easy"; // Default mode for Quiz
  let questionAmount = "8"; // Default question amount for Quiz
  let quizStarted = false;
  let timerEnabled = false; // Timer toggle state for Quiz
  let timerDuration = "15";
  let questionType = "any"; // Default question type for Quiz
  let showPlayQuizContainer = false; // Control visibility of play quiz container

  function handleStart() {
    quizStarted = true; // Start the quiz
  }

  function handleNewGame() {
    quizStarted = false; // Reset to go back to the selection screen
  }

  function togglePlayQuizContainer() {
    showPlayQuizContainer = !showPlayQuizContainer; // Toggle visibility of play quiz container
  }
</script>

<main class="main-container">
  {#if showPlayQuizContainer && !quizStarted}
    <div class="back-button" on:click={togglePlayQuizContainer}>
      <IconArrowNarrowLeft stroke={1} size={24} />
      <span>back</span>
    </div>
  {/if}

  {#if !quizStarted}
    {#if !showPlayQuizContainer}
      <div class="left-container">
        <div class="rectangle">
          <div class="icon-lock-container">
            <IconLock class="icon-lock" stroke={1} size={48} />
          </div>
          <div class="header">Multiplayer Mode</div>
          <div
            style="display: flex; justify-content: space-between; width: 100%; padding: 40px 30px; color: var(--dark-color);"
          >
            <IconUser stroke={1} size={60} />
            <IconSwords stroke={1} size={60} />
            <IconUser stroke={1} size={60} />
          </div>
          <div class="subtitle">Play with other players</div>
          <div class="rectangle-thick-line"></div>
          <div class="rectangle-description">
            Play with friends or random opponents
          </div>
        </div>
      </div>

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
              Ready to test your wildlife knowledge? Pick a difficulty—Easy,
              Medium, or Hard—and explore the animal kingdom with fun facts and
              surprises. Use the Joker to get hints if you’re stuck! Whether
              you're a beginner or an expert, there's something for everyone!
            </p>
          </div>
        </div>
      </div>

      <div class="right-container" on:click={togglePlayQuizContainer}>
        <div class="rectangle right-rectangle">
          <div class="header">Single Player Mode</div>
          <div class="icon-user-container">
            <IconUser stroke={1} size={60} />
          </div>
          <div class="subtitle">Play solo</div>
          <div class="rectangle-thick-line"></div>
          <div class="rectangle-description">
            Test your knowledge and learn facts
          </div>
        </div>
      </div>
    {/if}

    {#if showPlayQuizContainer}
      <div class="play-quiz-container">
        <div class="selection-container">
          <div class="selection-item">
            <label for="mode">Mode:</label>
            <div class="select">
              <div class="selected">
                {selectedMode.charAt(0).toUpperCase() + selectedMode.slice(1)}
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  height="1em"
                  viewBox="0 0 512 512"
                  class="arrow"
                >
                  <path
                    d="M233.4 406.6c12.5 12.5 32.8 12.5 45.3 0l192-192c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L256 338.7 86.6 169.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l192 192z"
                  ></path>
                </svg>
              </div>
              <div class="options">
                <div>
                  <input
                    id="easy"
                    name="mode"
                    type="radio"
                    bind:group={selectedMode}
                    value="easy"
                  /><label class="option" for="easy" data-txt="Easy"></label>
                </div>
                <div>
                  <input
                    id="medium"
                    name="mode"
                    type="radio"
                    bind:group={selectedMode}
                    value="medium"
                  /><label class="option" for="medium" data-txt="Medium"
                  ></label>
                </div>
                <div>
                  <input
                    id="hard"
                    name="mode"
                    type="radio"
                    bind:group={selectedMode}
                    value="hard"
                  /><label class="option" for="hard" data-txt="Hard"></label>
                </div>
              </div>
            </div>
          </div>

          <div class="selection-item">
            <label for="amount">Question Amount</label>
            <div class="select">
              <div class="selected">
                {questionAmount}
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  height="1em"
                  viewBox="0 0 512 512"
                  class="arrow"
                >
                  <path
                    d="M233.4 406.6c12.5 12.5 32.8 12.5 45.3 0l192-192c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L256 338.7 86.6 169.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l192 192z"
                  ></path>
                </svg>
              </div>
              <div class="options">
                <div>
                  <input
                    id="amount-8"
                    name="amount"
                    type="radio"
                    bind:group={questionAmount}
                    value="8"
                  /><label class="option" for="amount-8" data-txt="8"></label>
                </div>
                <div>
                  <input
                    id="amount-12"
                    name="amount"
                    type="radio"
                    bind:group={questionAmount}
                    value="12"
                  /><label class="option" for="amount-12" data-txt="12"></label>
                </div>
                <div>
                  <input
                    id="amount-16"
                    name="amount"
                    type="radio"
                    bind:group={questionAmount}
                    value="16"
                  /><label class="option" for="amount-16" data-txt="16"></label>
                </div>
              </div>
            </div>
          </div>

          <div class="selection-item">
            <label for="type">Question Type:</label>
            <div class="select">
              <div class="selected">
                {questionType.charAt(0).toUpperCase() +
                  questionType.slice(1).replace(/_/g, " ")}
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  height="1em"
                  viewBox="0 0 512 512"
                  class="arrow"
                >
                  <path
                    d="M233.4 406.6c12.5 12.5 32.8 12.5 45.3 0l192-192c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L256 338.7 86.6 169.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l192 192z"
                  ></path>
                </svg>
              </div>
              <div class="options">
                <div>
                  <input
                    id="any"
                    name="type"
                    type="radio"
                    bind:group={questionType}
                    value="any"
                  /><label class="option" for="any" data-txt="Any Type"></label>
                </div>
                <div>
                  <input
                    id="multiple_choice"
                    name="type"
                    type="radio"
                    bind:group={questionType}
                    value="multiple_choice"
                  /><label
                    class="option"
                    for="multiple_choice"
                    data-txt="Multiple Choice"
                  ></label>
                </div>
                <div>
                  <input
                    id="true_false"
                    name="type"
                    type="radio"
                    bind:group={questionType}
                    value="true_false"
                  /><label class="option" for="true_false" data-txt="True/False"
                  ></label>
                </div>
                <div>
                  <input
                    id="comparison"
                    name="type"
                    type="radio"
                    bind:group={questionType}
                    value="comparison"
                  /><label class="option" for="comparison" data-txt="Comparison"
                  ></label>
                </div>
              </div>
            </div>
          </div>

          <div class="selection-item">
            <div class="timer-container">
              <label for="timer">Enable Timer:</label>
              <label class="switch">
                <input type="checkbox" id="timer" bind:checked={timerEnabled} />
                <span class="slider">
                  <svg
                    class="slider-icon"
                    viewBox="0 0 32 32"
                    xmlns="http://www.w3.org/2000/svg"
                    aria-hidden="true"
                    role="presentation"
                  >
                    <path fill="none" d="m4 16.5 8 8 16-16"></path>
                  </svg>
                </span>
              </label>
            </div>

            <div class="select" class:disabled={!timerEnabled}>
              <div class="selected">
                {timerDuration}s
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  height="1em"
                  viewBox="0 0 512 512"
                  class="arrow"
                >
                  <path
                    d="M233.4 406.6c12.5 12.5 32.8 12.5 45.3 0l192-192c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L256 338.7 86.6 169.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l192 192z"
                  ></path>
                </svg>
              </div>
              <div class="options">
                <div>
                  <input
                    id="timer-5"
                    name="timerDuration"
                    type="radio"
                    bind:group={timerDuration}
                    value="5"
                    disabled={!timerEnabled}
                  /><label class="option" for="timer-5" data-txt="5s"></label>
                </div>
                <div>
                  <input
                    id="timer-10"
                    name="timerDuration"
                    type="radio"
                    bind:group={timerDuration}
                    value="10"
                    disabled={!timerEnabled}
                  /><label class="option" for="timer-10" data-txt="10s"></label>
                </div>
                <div>
                  <input
                    id="timer-15"
                    name="timerDuration"
                    type="radio"
                    bind:group={timerDuration}
                    value="15"
                    disabled={!timerEnabled}
                  /><label class="option" for="timer-15" data-txt="15s"></label>
                </div>
                <div>
                  <input
                    id="timer-20"
                    name="timerDuration"
                    type="radio"
                    bind:group={timerDuration}
                    value="20"
                    disabled={!timerEnabled}
                  /><label class="option" for="timer-20" data-txt="20s"></label>
                </div>
              </div>
            </div>
          </div>
        </div>

        <button class="start-button" on:click={handleStart}>Start Quiz</button>
      </div>
    {/if}
  {/if}

  {#if quizStarted}
    <div class="quiz-game-container">
      <QuizGame
        mode={selectedMode}
        amount={parseInt(questionAmount)}
        type={questionType}
        {timerEnabled}
        {timerDuration}
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

  .left-container,
  .right-container {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .rectangle {
    position: relative;
    flex-grow: 0;
    width: 320px;
    height: 470px;
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

  .right-rectangle {
    background-color: var(--website-green-color); /* Change to desired color */
    cursor: pointer;
  }

  .right-container:hover .right-rectangle {
    transform: translateY(-10px);
    box-shadow: 0 6px 12px rgba(32, 255, 203, 0.1);
  }

  .icon-user-container {
    display: flex;
    justify-content: center;
    width: 100%;
    padding: 40px 30px;
    color: var(--dark-color);
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
    font-size: 1rem;
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
    margin: 0 2rem; /* Add margin to create equal gaps */
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

  .play-quiz-container {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: start;
    gap: 4rem;
  }

  .selection-container {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 2rem;
    text-align: center;
    border-radius: 8px;
    padding: 1rem;
  }

  label {
    display: block;
    margin-bottom: 1rem;
  }

  .select {
    width: fit-content;
    cursor: pointer;
    position: relative;
    transition: 300ms;
    color: white;
    overflow: hidden;
  }

  .selected {
    background-color: #2a2f3b;
    padding: 0.5rem 1rem;
    margin-bottom: 3px;
    border-radius: 5px;
    position: relative;
    z-index: 100000;
    font-size: 15px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .arrow {
    position: relative;
    right: 0px;
    height: 10px;
    transform: rotate(-90deg);
    width: 25px;
    fill: white;
    z-index: 100000;
    transition: 300ms;
  }

  .options {
    display: flex;
    flex-direction: column;
    border-radius: 5px;
    padding: 5px;
    background-color: #2a2f3b;
    position: relative;
    top: -100px;
    opacity: 0;
    transition: 300ms;
  }

  .select:hover > .options {
    opacity: 1;
    top: 0;
  }

  .select:hover > .selected .arrow {
    transform: rotate(0deg);
  }

  .option {
    border-radius: 5px;
    padding: 5px;
    transition: 300ms;
    background-color: #2a2f3b;
    width: 150px;
    font-size: 15px;
  }

  .options input[type="radio"] {
    display: none;
  }

  .options label::before {
    content: attr(data-txt);
  }

  /* Remove the hide logic for selected options */
  .options input[type="radio"]:checked + label {
    background-color: #323741; /* Give the selected option a distinct color */
    color: white;
  }

  /* Option hover effect */
  .option:hover {
    background-color: #323741;
  }

  /* Make sure all options are visible, even after selection */
  .options label {
    display: inline-block;
    padding: 5px;
  }

  .options input[type="radio"]#all:checked + label {
    display: none;
  }

  .select:has(.options input[type="radio"]#all:checked) .selected::before {
    content: attr(data-default);
  }
  .select:has(.options input[type="radio"]#option-1:checked) .selected::before {
    content: attr(data-one);
  }
  .select:has(.options input[type="radio"]#option-2:checked) .selected::before {
    content: attr(data-two);
  }
  .select:has(.options input[type="radio"]#option-3:checked) .selected::before {
    content: attr(data-three);
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

  .back-button {
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

  .back-button:hover {
    color: var(--website-green-color); /* Change background color on hover */
  }

  .back-button span {
    margin-left: 5px;
  }

  .timer-container {
    display: flex;
    align-items: flex-start;

    gap: 10px; /* Adjust the gap between the label and the switch */
  }

  /* The switch - the box around the slider */
  .switch {
    font-size: 0.8rem;
    position: relative;
    display: inline-block;
    width: 3.5em;
    height: 2em;
  }

  /* Hide default HTML checkbox */
  .switch input {
    opacity: 0;
    width: 0;
    height: 0;
  }

  /* The slider */
  .slider {
    position: absolute;
    cursor: pointer;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: #b0b0b0;
    border: 1px solid #b0b0b0;
    transition: 0.4s;
    border-radius: 32px;
    outline: none;
  }

  .slider:before {
    position: absolute;
    content: "";
    height: 1.7rem;
    width: 1.7rem;
    border-radius: 50%;
    outline: 2px solid #b0b0b0;
    left: -1px;
    bottom: -1px;
    background-color: #fff;
    transition: transform 0.25s ease-in-out 0s;
  }

  .slider-icon {
    opacity: 0;
    height: 12px;
    width: 12px;
    stroke-width: 8;
    position: absolute;
    z-index: 999;
    stroke: #222222;
    right: 70%;
    top: 20%;
    transition:
      right ease-in-out 0.3s,
      opacity ease-in-out 0.15s;
  }

  input:checked + .slider {
    background-color: #222222;
  }

  input:checked + .slider .slider-icon {
    opacity: 1;
    right: 15%;
  }

  input:checked + .slider:before {
    transform: translateX(1.5em);
    outline-color: #181818;
  }
</style>
