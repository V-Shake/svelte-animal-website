<script>
  import TigerModel from "../components/TigerModel.svelte";
  import { onMount } from "svelte";

  let activeSection = 0;
  let scrollTimeout;

  function handleScroll(event) {
    clearTimeout(scrollTimeout);
    scrollTimeout = setTimeout(() => {
      const delta = Math.sign(event.deltaY);
      const newSection = Math.max(0, Math.min(1, activeSection + delta));
      if (newSection !== activeSection) {
        activeSection = newSection;
        document.getElementById(`section-${activeSection}`)?.scrollIntoView({ behavior: "smooth" });

        if (activeSection === 0) {
          document.dispatchEvent(new Event('rotateToFrontView'));
        } else if (activeSection === 1) {
          document.dispatchEvent(new Event('rotateToSideView'));
        }
      }
    }, 100); // Adjust the timeout duration as needed
  }

  onMount(() => {
    document.addEventListener('wheel', handleScroll);
  });
</script>

<div id="fullpage">
  <div id="section-0" class="section">
    <TigerModel />
    <h1>Front View</h1>
  </div>
  <div id="section-1" class="section">
    <TigerModel />
    <h1>Side View</h1>
  </div>
</div>

<style>
  #fullpage {
    height: 100vh;
    width: 100vw;
    overflow: hidden;
  }
  .section {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    scroll-snap-align: start;
  }
</style>