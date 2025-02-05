<script>
    import { onMount } from "svelte";
    // import TigerModel from "./TigerModel.svelte";
  
    let sections = [
      { id: "home", content: "Welcome to the Tiger Page!" },
      { id: "tiger", content: "This is the Tiger Model Section" },
    ];
  
    let activeSection = 0;
  
    function handleScroll(event) {
      const delta = Math.sign(event.deltaY);
      activeSection = Math.max(0, Math.min(sections.length - 1, activeSection + delta));
      document.getElementById(sections[activeSection].id)?.scrollIntoView({ behavior: "smooth" });
    }
  
    // Trigger the tiger rotation when the tiger section is active
    const isTigerSection = () => sections[activeSection]?.id === "tiger";
  </script>
  
  <div on:wheel="{handleScroll}">
    {#each sections as section, i}
      <section id={section.id} class={isTigerSection() && i === activeSection ? 'active' : ''}>
        {i === 1 ? <TigerModel rotateTiger={isTigerSection()} /> : section.content}
      </section>
    {/each}
  </div>
  
  <style>
    section {
      height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      scroll-snap-align: start;
      
    }
  
    div {
      overflow: hidden;
      scroll-snap-type: y mandatory;
    }
  </style>
  