<script>
    export let activeSection = 0;
  </script>
  
  <div class="navigation">
    <div class="line {activeSection === 0 ? 'active' : ''}"></div>
    <div class="line {activeSection === 1 ? 'active' : ''}"></div>
  </div>
  
  <style>
    .navigation {
      position: fixed;
      right: 6.5rem;
      top: 50%;
      transform: translateY(-50%);
      display: flex;
      flex-direction: column;
      align-items: flex-end;
      gap: 1em;
    }
  
    .line {
      width: 30px; /* Initial width */
      height: 2px;
      background-color: white;
      position: relative;
      overflow: hidden;
      transform-origin: right; /* Ensure the animation grows towards the left */
      border-radius: 1px; /* Make the line rounded */
    }
  
    .line.active {
      background-color: transparent; /* Hide the original white line */
      width: 45px; /* Make the activated line longer */
    }
  
    .line.active::after {
      content: "";
      position: absolute;
      bottom: 0;
      right: 0;
      width: 0;
      height: 2px;
      background-color: var(--website-green-color);
      animation: underline 0.5s ease forwards;
      transform-origin: right; /* Ensure the animation starts from the right */
      border-radius: 1px; /* Make the animated line rounded */
    }
  
    @keyframes underline {
      from {
        width: 0;
      }
      to {
        width: 100%; /* The length will grow to the full width */
      }
    }
  </style>