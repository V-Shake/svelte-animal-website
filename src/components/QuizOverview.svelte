<script>
  import { onMount } from "svelte";
  import { gsap } from "gsap";
  import * as THREE from "three";
  import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
  import confetti from "canvas-confetti";

  export let userAnswers = [];
  export let playAgain;
  export let newGame;

  // Calculate the result message based on the percentage of correct answers
  function getResultMessage() {
    const correctAnswers = userAnswers.filter(
      (answer) => answer.isCorrect,
    ).length;
    const percentage = (correctAnswers / userAnswers.length) * 100;

    if (percentage === 100) {
      return `You’re a Wildlife Guru!`;
    } else if (percentage >= 80) {
      return `You’re a Wildlife Expert!`;
    } else if (percentage >= 60) {
      return `You’re a Wildlife Explorer!`;
    } else if (percentage >= 40) {
      return `You’re a Wildlife Adventurer!`;
    } else if (percentage >= 20) {
      return `You’re a Wildlife Wanderer!`;
    } else {
      return `You’re a Wildlife Newbie!`;
    }
  }

  // Get the fun tip based on the percentage of correct answers
  function getFunTip() {
    const correctAnswers = userAnswers.filter(
      (answer) => answer.isCorrect,
    ).length;
    const percentage = (correctAnswers / userAnswers.length) * 100;

    if (percentage === 100) {
      return "You’ve earned the crown of an explorer!";
    } else if (percentage >= 80) {
      return "Impressive! You’ve shown you have a deep understanding of wildlife.";
    } else if (percentage >= 60) {
      return "You’ve got a solid understanding of wildlife, but there’s still more to discover!";
    } else if (percentage >= 40) {
      return "Not bad! You’ve got the basics down, but there’s plenty more to uncover.";
    } else if (percentage >= 20) {
      return "You’re just starting your wildlife journey. There’s so much to learn, and you’re on the right track!";
    } else {
      return "Looks like you’ve just started your wildlife exploration! Don’t worry; every expert was once a beginner.";
    }
  }

  // Calculate the percentage of correct answers
  function getCorrectPercentage() {
    const correctAnswers = userAnswers.filter(
      (answer) => answer.isCorrect,
    ).length;
    const percentage = (correctAnswers / userAnswers.length) * 100;
    return Math.round(percentage);
  }

  onMount(() => {
    // Set up the scene, camera, and renderer
    const container = document.querySelector(".three-container");
    const { clientWidth, clientHeight } = container;

    // ✅ Initialize Scene, Camera, and Renderer
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      75,
      clientWidth / clientHeight,
      0.1,
      1000,
    );
    const renderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });

    // ✅ Set Renderer Size Based on Container
    renderer.setSize(clientWidth, clientHeight);
    container.appendChild(renderer.domElement);

    // ✅ 1. Add Ambient Light (soft background light to avoid darkness)
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.05); // Soft light
    scene.add(ambientLight);

    // ✅ 2. Add Directional Light (like sunlight)
    const directionalLight = new THREE.DirectionalLight(0xffffff, 2);
    directionalLight.position.set(20, -16, 25); // Light coming from top-front
    directionalLight.castShadow = true; // Enable shadows
    scene.add(directionalLight);

    // ✅ 3. Add a Point Light (for highlights on the crown)
    const pointLight = new THREE.PointLight(0xffffff, 2, 10);
    pointLight.position.set(-10, 15, 10); // Slightly above and in front
    scene.add(pointLight);

    // Load the 3D model
    const loader = new GLTFLoader();
    loader.load("src/assets/models/crown.glb", (gltf) => {
      const model = gltf.scene;
      model.scale.set(0, 0, 0); // Start with scale 0
      model.position.set(0, 0, 0); // Center it
      scene.add(model);

      // Fix model lighting issues (ensure materials react to light)
      model.traverse((child) => {
        if (child.isMesh) {
          child.castShadow = true;
          child.receiveShadow = true;
          child.material.metalness = 0.9; // Make it shiny
          child.material.roughness = 0.1; // Reduce roughness for a polished look
        }
      });

      confetti({
        particleCount: 100,
        spread: 170,
        origin: { y: 0.6 },
      });

      // Animate the model appearing
      gsap
        .to(model.scale, { x: 5, y: 5, z: 5, duration: 2, ease: "elastic.out" })
        .then(() => {
          gsap.to(".hidden-content", { opacity: 1, duration: 1, delay: 0.5 });
        });
      // Rotate the model continuously
      gsap.to(model.rotation, {
        y: "+=6.28319",
        duration: 40,
        ease: "linear",
        repeat: -1,
      }); // 2 * Math.PI for a full rotation
    });

    camera.position.set(0, 2, 5); // Slightly higher for a better view
    camera.lookAt(0, 2.5, 0);

    // Enable shadows
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;

    // Animation loop
    function animate() {
      requestAnimationFrame(animate);
      renderer.render(scene, camera);
    }
    animate();

    // Animate the circular chart
    const percentage = getCorrectPercentage();
    gsap.fromTo(
      ".circle",
      { strokeDasharray: "0, 100" },
      {
        strokeDasharray: `${percentage}, 100`,
        duration: 3,
        ease: "easeOut",
        delay: 3,
      },
    );
  });
</script>

<div class="overview">
  <div class="three-container" style="width: 100%; height: 400px;"></div>
  <div class="hidden-content">
    <div class="result-container">
      <div class="progress-circle">
        <svg viewBox="0 0 36 36" class="circular-chart">
          <path
            class="circle-bg"
            d="M18 2.0845
              a 15.9155 15.9155 0 0 1 0 31.831
              a 15.9155 15.9155 0 0 1 0 -31.831"
          />
          <path
            class="circle"
            stroke-dasharray="{getCorrectPercentage()}, 100"
            d="M18 2.0845
              a 15.9155 15.9155 0 0 1 0 31.831
              a 15.9155 15.9155 0 0 1 0 -31.831"
          />
          <text x="18" y="20.35" class="percentage"
            >{getCorrectPercentage()}%</text
          >
        </svg>
      </div>
      <div class="result-text">
        <h2>{getResultMessage()}</h2>
        <p class="fun-tip">{getFunTip()}</p>
      </div>
    </div>

    <table>
      <thead>
        <tr>
          <th>Number</th>
          <th>Question</th>
          <th>Your Answer</th>
          <th>Correct Answer</th>
        </tr>
      </thead>
      <tbody>
        {#each userAnswers as answer, index}
          <tr>
            <td>{index + 1}</td>
            <td>{@html answer.question}</td>
            <td class={answer.isCorrect ? "correct" : "incorrect"}>
              {@html typeof answer.selectedAnswer === "object"
                ? answer.selectedAnswer.name
                : answer.selectedAnswer}
            </td>
            <td>
              {@html typeof answer.correctAnswer === "object"
                ? answer.correctAnswer.name
                : answer.correctAnswer}
            </td>
          </tr>
        {/each}
      </tbody>
    </table>
    <button class="play-again-button" on:click={playAgain}>Play Again</button>
    <button class="new-game-button" on:click={newGame}>New Game</button>
  </div>
</div>

<style>
  .overview {
    padding: 20px;
    margin: 30rem auto 0;
    max-width: 1000px;
  }

  .three-container {
    flex: 1;
    margin-top: -8rem;
  }

  .hidden-content {
    flex: 1;
    opacity: 0;
  }

  .result-container {
    display: flex;
    align-items: center; /* Align items vertically */
    gap: 2rem; /* Add space between the circle and text */
    justify-content: center; /* Center the whole container */
    margin-bottom: -1rem;
  }

  .result-text {
    max-width: 400px; /* Limit text width for balance */
    text-align: left;
  }

  .progress-circle {
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 3rem 0;
  }

  .circular-chart {
    display: block;
    margin: 10px auto;
    max-width: 80%;
    height: 150px;
    transform-origin: center;
  }

  .circle-bg {
    fill: none;
    stroke: var(--website-dark-green-color);
    stroke-width: 3;
  }

  .circle {
    fill: none;
    stroke-width: 2.5;
    stroke-linecap: round;
  }

  .circle {
    stroke: var(--website-green-color);
  }

  .percentage {
    fill: var(--website-green-color);
    font-size: 0.5em;
    text-anchor: middle;
  }

  @keyframes progress {
    0% {
      stroke-dasharray: 0 100;
    }
  }

  .hidden-content {
    opacity: 0;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 1rem;
    font-size: 0.8rem;
    font-weight: 100;
    color: rgb(202, 202, 202);
  }

  th,
  td {
    border: 1px solid rgb(28, 28, 28);
    padding: 0.5rem;
    text-align: left;
  }

  th {
    background-color: var(--dark-color);
    padding: 1rem 0.5rem;
    color: var(--card-background-color);
   font-weight: 100;
  }

  td.correct {
    color: var(--quiz-correct-color);
  }

  td.incorrect {
    color: var(--quiz-wrong-color);
  }

  .play-again-button,
  .new-game-button {
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

  .play-again-button:hover,
  .new-game-button:hover {
    background-color: #0056b3;
  }

  .fun-tip {
    font-size: 0.9rem;
    color: #666;
    margin-bottom: 1rem;
  }
</style>
