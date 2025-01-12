<script>
  import TigerModel from "../components/TigerModel.svelte";
  import SectionNavigation from "../components/SectionNavigation.svelte";
  import { onMount } from "svelte";
  import { gsap } from "gsap";

  let activeSection = 0;
  let scrollTimeout;

  function handleScroll(event) {
    clearTimeout(scrollTimeout);
    scrollTimeout = setTimeout(() => {
      const delta = Math.sign(event.deltaY);
      const newSection = Math.max(0, Math.min(1, activeSection + delta));
      if (newSection !== activeSection) {
        activeSection = newSection;
        document
          .getElementById(`section-${activeSection}`)
          ?.scrollIntoView({ behavior: "smooth" });

        if (activeSection === 0) {
          document.dispatchEvent(new Event("rotateToFrontView"));
        } else if (activeSection === 1) {
          document.dispatchEvent(new Event("rotateToSideView"));
        }
      }
    }, 50); // Reduced timeout duration for quicker response
  }

  onMount(() => {
    document.addEventListener("wheel", handleScroll);

    // Create a GSAP timeline
    const tl = gsap.timeline();

    // Animate the letters of "Wildlife" first
    tl.fromTo(
      ".wildlife span",
      { opacity: 0, y: 20 },
      { opacity: 1, y: 0, duration: 1, stagger: 0.1, ease: "power2.out" },
    );

    // Animate the letters of "Exploration" immediately after "Wildlife" animation is finished
    tl.fromTo(
      ".exploration span",
      { opacity: 0, y: 20 },
      { opacity: 1, y: 0, duration: 1, stagger: 0.1, ease: "power2.out" },
      "-=0.8", // Overlap the animations by 0.5 seconds
    );
  });
</script>

<div id="fullpage">
  <div id="model-container">
    <TigerModel />
  </div>
  <div id="section-0" class="section">
    <h1 class="left fade-in wildlife">
      {#each "Wildlife".split("") as letter, index}
        <span style="--index: {index}">{letter}</span>
      {/each}
    </h1>
    <h1 class="right fade-in exploration">
      {#each "Exploration".split("") as letter, index}
        <span style="--index: {index}">{letter}</span>
      {/each}
    </h1>
  </div>
  <div id="section-1" class="section">
    <h1 class="fade-in right-text">
      <div class="line">
        {#each "32".split("") as letter, index}
          <span style="--index: {index}">{letter}</span>
        {/each}
      </div>
      <div class="line">
        {#each "Wildlife".split("") as letter, index}
          <span style="--index: {index}">{letter}</span>
        {/each}
      </div>
    </h1>
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
    pointer-events: auto; /* Allow interactions */
  }
  .section {
    display: flex;
    justify-content: center;
    align-items: center;
    height: calc(100vh - 5em); /* Subtract the height of the navbar */
    scroll-snap-align: start; /* Snap to the start of each section */
    overflow: hidden; /* Hide overflow content */
    position: relative;
    pointer-events: none;
  }
  #section-1 {
    pointer-events: auto;
  }
  h1 {
    font-size: 6em;
    font-weight: 300;
    color: rgba(255, 255, 255, 0.8);
    z-index: -1;
    display: inline-block;
  }
  h1.left {
    position: absolute;
    left: 6.5rem;
    top: 50%;
    transform: translateY(-50%);
    font-size: 8em;
  }
  h1.right {
    position: absolute;
    right: 6.5rem;
    top: 55%;
    transform: translateY(-50%);
  }
  h1.right-text {
    position: absolute;
    right: 15rem;
    top: 40%;
    transform: translateY(-50%);
    font-size: 5em;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    gap: 0.2em;
  }

  h1.right-text .line {
    display: inline-block;
    margin-bottom: -0.4em; /* Negative margin reduces space */
  }

  .fade-in span {
    display: inline-block;
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
</style>
