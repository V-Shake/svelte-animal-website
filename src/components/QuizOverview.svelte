<script>
    export let userAnswers = [];
    export let playAgain;
    export let newGame;
  
    // Calculate the result message based on the percentage of correct answers
    function getResultMessage() {
      const correctAnswers = userAnswers.filter(answer => answer.isCorrect).length;
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
      const correctAnswers = userAnswers.filter(answer => answer.isCorrect).length;
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
      const correctAnswers = userAnswers.filter(answer => answer.isCorrect).length;
      const percentage = (correctAnswers / userAnswers.length) * 100;
      return Math.round(percentage);
    }
  </script>
  
  <div class="overview">
    <h2>{getResultMessage()}</h2>
    <p class="fun-tip">{getFunTip()}</p>
    <div class="progress-circle">
      <svg viewBox="0 0 36 36" class="circular-chart">
        <path class="circle-bg"
          d="M18 2.0845
            a 15.9155 15.9155 0 0 1 0 31.831
            a 15.9155 15.9155 0 0 1 0 -31.831"
        />
        <path class="circle"
          stroke-dasharray="{getCorrectPercentage()}, 100"
          d="M18 2.0845
            a 15.9155 15.9155 0 0 1 0 31.831
            a 15.9155 15.9155 0 0 1 0 -31.831"
        />
        <text x="18" y="20.35" class="percentage">{getCorrectPercentage()}%</text>
      </svg>
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
              {@html typeof answer.selectedAnswer === 'object' ? answer.selectedAnswer.name : answer.selectedAnswer}
            </td>
            <td>
              {@html typeof answer.correctAnswer === 'object' ? answer.correctAnswer.name : answer.correctAnswer}
            </td>
          </tr>
        {/each}
      </tbody>
    </table>
    <button class="play-again-button" on:click={playAgain}>Play Again</button>
    <button class="new-game-button" on:click={newGame}>New Game</button>
  </div>
  
  <style>
    .progress-circle {
      margin: 1rem 0;
    }
  
    .circular-chart {
      display: block;
      margin: 10px auto;
      max-width: 80%;
      max-height: 250px;
    }
  
    .circle-bg {
      fill: none;
      stroke: #eee;
      stroke-width: 3.8;
    }
  
    .circle {
      fill: none;
      stroke-width: 2.8;
      stroke-linecap: round;
      animation: progress 1.5s ease-out forwards;
    }
  
    .circle {
      stroke: #4caf50;
    }
  
    .percentage {
      fill: #666;
      font-size: 0.5em;
      text-anchor: middle;
    }
  
    @keyframes progress {
      0% {
        stroke-dasharray: 0 100;
      }
    }
  
    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 1rem;
    }
  
    th, td {
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
  
    .play-again-button, .new-game-button {
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
  
    .play-again-button:hover, .new-game-button:hover {
      background-color: #0056b3;
    }
  
    .fun-tip {
      font-size: 0.9rem;
      color: #666;
      margin-bottom: 1rem;
    }
  </style>