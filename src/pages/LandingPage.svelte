<script>
  import TigerModel from "../components/TigerModel.svelte";
  import SectionNavigation from "../components/SectionNavigation.svelte";
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
    }, 50); // Reduced timeout duration for quicker response
  }

  onMount(() => {
    document.addEventListener('wheel', handleScroll);
  });
</script>

<div id="fullpage">
  <div id="model-container">
    <TigerModel />
  </div>
  <div id="section-0" class="section">
    <h1>Front View</h1>
  </div>
  <div id="section-1" class="section">
    <h1>Side View</h1>
  </div>
  <SectionNavigation {activeSection} />
</div>

<style>
  #fullpage {
    height: calc(100vh - 5em); /* Subtract the height of the navbar */
    width: 100vw;
    overflow: hidden;
    scroll-snap-type: y mandatory; /* Enable scroll snapping */
    position: relative;
  }
  #model-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    pointer-events: none; /* Allow interactions to pass through */
  }
  .section {
    display: flex;
    justify-content: center;
    align-items: center;
    height: calc(100vh - 5em); /* Subtract the height of the navbar */
    scroll-snap-align: start; /* Snap to the start of each section */
    overflow: hidden; /* Hide overflow content */
    position: relative;
    z-index: 1; /* Ensure sections are above the model */
  }
</style>