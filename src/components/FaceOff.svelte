<script>
    import { onMount } from "svelte";
    import animalData from "../assets/animaldata.js";
  
    export let mode = "easy"; // Default mode
    export let amount = 5; // Default question amount
    export let timerEnabled = false; // Timer toggle state
  
    let currentAnimals = [];
    let question = "";
    let comparisonAttribute = "";
    let showComparison = false;
    let correctAnimal = null;
    let timer = 30; // Timer countdown in seconds
    let timerInterval;
    let userAnswers = []; // Track user answers
    let currentQuestionIndex = 0; // Track the current question index
    let faceOffCompleted = false; // Flag to show the overview after the last question
  
    function getRandomAnimals() {
      const shuffled = [...animalData].sort(() => 0.5 - Math.random()); // Create a copy and sort it
      const numAnimals = mode === "easy" ? 2 : mode === "medium" ? 3 : 4;
      return shuffled.slice(0, numAnimals);
    }
  
    function generateQuestion() {
      const attributes = [
        "top_speed",
        "max_weight",
        "max_length",
        "intelligence",
        "max_age",
        "deaths",
        "litter_size",
      ];
      comparisonAttribute =
        attributes[Math.floor(Math.random() * attributes.length)];
      question = `Which one has ${
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
      correctAnimal = currentAnimals.reduce((prev, current) =>
        current[comparisonAttribute] > prev[comparisonAttribute] ? current : prev
      );
    }
  
    function startFaceOff() {
      if (currentQuestionIndex < amount) {
        currentAnimals = getRandomAnimals();
        generateQuestion();
        showComparison = false;
  
        if (timerEnabled) {
          startTimer();
        }
      } else {
        faceOffCompleted = true; // Show the overview after the last question
      }
    }
  
    function showStats(selectedAnimal) {
      showComparison = true;
      if (timerEnabled) {
        clearInterval(timerInterval);
      }
  
      // Record the user's answer and whether it was correct
      userAnswers = [
        ...userAnswers,
        {
          question,
          selectedAnimal,
          correctAnimal,
          isCorrect: selectedAnimal === correctAnimal,
        },
      ];
  
      currentQuestionIndex++;
    }
  
    function startTimer() {
      timer = 30;
      timerInterval = setInterval(() => {
        timer--;
        if (timer <= 0) {
          clearInterval(timerInterval);
          showStats();
        }
      }, 1000);
    }
  
    onMount(() => {
      startFaceOff();
    });
  
    function playAgain() {
      currentQuestionIndex = 0;
      userAnswers = [];
      faceOffCompleted = false;
      startFaceOff();
    }
  </script>
  
  <main class="faceoff-container">
    {#if !faceOffCompleted}
      <div class="question-container">
        <h3>{question}</h3>
        {#if timerEnabled}
          <div class="timer">
            <p>Time Remaining: {timer} seconds</p>
          </div>
        {/if}
        <div class="animals">
          {#each currentAnimals as animal}
            <div class="animal">
              <img
                src={`/images/${animal.group.toLowerCase()}${animal.group_number}.png`}
                alt={animal.name}
              />
              <p>{animal.name}</p>
              {#if !showComparison}
                <button on:click={() => showStats(animal)}>Select</button>
              {/if}
              {#if showComparison}
                <p>
                  {comparisonAttribute.charAt(0).toUpperCase() +
                    comparisonAttribute.slice(1).replace("_", " ")}: {animal[
                    comparisonAttribute
                  ]}
                </p>
                {#if animal === correctAnimal}
                  <p class="correct">Correct!</p>
                {/if}
              {/if}
            </div>
          {/each}
        </div>
        {#if showComparison}
          <button on:click={startFaceOff}>Next Comparison</button>
        {/if}
      </div>
    {:else}
      <!-- Overview Section -->
      <div class="overview">
        <h2>Face-Off Overview</h2>
        <table>
          <thead>
            <tr>
              <th>Number</th>
              <th>Question</th>
              <th>Your Answer</th>
              <th>Correct Answer</th>
              <th>Result</th>
            </tr>
          </thead>
          <tbody>
            {#each userAnswers as answer, index}
              <tr>
                <td>{index + 1}</td>
                <td>{@html answer.question}</td>
                <td>{@html answer.selectedAnimal.name}</td>
                <td>{@html answer.correctAnimal.name}</td>
                <td class={answer.isCorrect ? "correct" : "incorrect"}>
                  {answer.isCorrect ? "Correct" : "Incorrect"}
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
        <button class="play-again-button" on:click={playAgain}>Play Again</button>
      </div>
    {/if}
  </main>
  
  <style>
    .faceoff-container {
      text-align: center;
      padding: 2rem;
    }
  
    .question-container,
    .comparison-container,
    .overview {
      display: flex;
      flex-direction: column;
      align-items: center;
    }
  
    .animals {
      display: flex;
      justify-content: space-evenly;
      width: 100%;
      gap: 0.5rem; /* Adjust the gap between the options */
    }
  
    .animal {
      text-align: center;
    }
  
    .animal img {
      width: 342px;
      height: 320px;
      object-fit: cover; /* Ensure the image is not squished */
    }
  
    button {
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
  
    button:hover {
      background-color: #0056b3;
    }
  
    .correct {
      color: green;
      font-weight: bold;
    }
  
    .incorrect {
      color: red;
      font-weight: bold;
    }
  
    .timer {
      margin-bottom: 1rem;
      font-size: 1.2rem;
      font-weight: bold;
    }
  
    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 1rem;
    }
  
    th,
    td {
      border: 1px solid #ccc;
      padding: 0.5rem;
      text-align: left;
    }
  
    th {
      background-color: #f0f0f0;
    }
  
    td.correct {
      color: green;
    }
  
    td.incorrect {
      color: red;
    }
  </style>