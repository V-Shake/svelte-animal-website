<script>
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher();
  
    function start() {
      dispatch("start");
    }
  </script>
  
  <button on:click={start}>Start</button>
  
  <style>
    button {
      padding: 0.5rem 1rem;
      background-color: var(--website-green-color);
      color: var(--dark-color);
      border: none;
      border-radius: 5px;
      cursor: pointer;
      font-size: 1rem;
      transition: all 0.3s ease;
    }
  
    button:hover {
      transform: translateY(-3px);
      box-shadow: 0 6px 12px rgba(32, 255, 203, 0.1);
    }
  </style>
  