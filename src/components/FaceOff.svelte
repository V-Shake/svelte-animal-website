<script>
    import { onMount } from "svelte";
    import animalData from "../assets/animaldata.js";
  
    let currentAnimals = [];
    let question = "";
    let comparisonAttribute = "";
    let showComparison = false;
    let correctAnimal = null;
  
    function getRandomAnimals() {
      const shuffled = animalData.sort(() => 0.5 - Math.random());
      return shuffled.slice(0, 2);
    }
  
    function generateQuestion() {
      const attributes = ["top_speed", "max_weight", "max_length", "intelligence"];
      comparisonAttribute = attributes[Math.floor(Math.random() * attributes.length)];
      question = `Which one is ${comparisonAttribute === "top_speed" ? "faster" : comparisonAttribute === "max_weight" ? "heavier" : comparisonAttribute === "max_length" ? "longer" : "smarter"}?`;
      correctAnimal = currentAnimals.reduce((prev, current) => (current[comparisonAttribute] > prev[comparisonAttribute] ? current : prev));
    }
  
    function startFaceOff() {
      currentAnimals = getRandomAnimals();
      generateQuestion();
      showComparison = false;
    }
  
    function showStats(selectedAnimal) {
      showComparison = true;
    }
  
    onMount(() => {
      startFaceOff();
    });
  </script>
  
  <main class="faceoff-container">
    {#if !showComparison}
      <div class="question-container">
        <h3>{question}</h3>
        <div class="animals">
          {#each currentAnimals as animal}
            <div class="animal">
              <img src={`/images/${animal.group.toLowerCase()}${animal.group_number}.png`} alt={animal.name} />
              <p>{animal.name}</p>
              <button on:click={() => showStats(animal)}>Select</button>
            </div>
          {/each}
        </div>
      </div>
    {:else}
      <div class="comparison-container">
        <h3>Comparison Results</h3>
        <div class="animals">
          {#each currentAnimals as animal}
            <div class="animal">
              <img src={`/images/${animal.group.toLowerCase()}${animal.group_number}.png`} alt={animal.name} />
              <p>{animal.name}</p>
              <p>{comparisonAttribute.charAt(0).toUpperCase() + comparisonAttribute.slice(1).replace('_', ' ')}: {animal[comparisonAttribute]}</p>
              {#if animal === correctAnimal}
                <p class="correct">Correct!</p>
              {/if}
            </div>
          {/each}
        </div>
        <button on:click={startFaceOff}>Next Comparison</button>
      </div>
    {/if}
  </main>
  
  <style>
    .faceoff-container {
      text-align: center;
      padding: 2rem;
    }
  
    .question-container, .comparison-container {
      display: flex;
      flex-direction: column;
      align-items: center;
    }
  
    .animals {
      display: flex;
      justify-content: space-between;
      width: 100%;
      gap: 1rem; /* Adjust the gap between the options */
    }
  
    .animal {
      text-align: center;
    }
  
    .animal img {
      width: 428px;
      height: 400px;
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
  </style>