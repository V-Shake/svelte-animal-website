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
      background-color: #007bff;
      color: #fff;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      font-size: 1rem;
    }
  
    button:hover {
      background-color: #0056b3;
    }
  </style>
  