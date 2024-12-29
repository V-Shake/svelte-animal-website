<script>
    import { onMount } from "svelte";

    // Scroll listener to detect scroll position
    let isScrolled = false;

    const handleScroll = () => {
        isScrolled = window.scrollY > 0; // Tracks scroll position
    };

    onMount(() => {
        window.addEventListener("scroll", handleScroll);
        return () => {
            window.removeEventListener("scroll", handleScroll);
        };
    });
</script>

<nav class={isScrolled ? "scrolled" : ""}>
    <div id="logo" class="font-white">My Logo</div>
    <ul>
        <li><a href="/" class="font-white">Home</a></li>
        <li><a href="#/cards" class="font-white">Cards</a></li>
    </ul>
</nav>

<style>
    nav {
      background-color: rgba(16, 16, 16, 0.93);
      display: flex;
      justify-content: space-between;
      align-items: center;
      width: 100%;
      position: fixed;
      top: 0;
      z-index: 1000;
      padding: 0.6rem 6.5rem;
      transition: background-color 0.3s ease;
      height: 4em; /* Set height for the navbar */
    }
  
    #logo {
      text-align: left;
      text-transform: uppercase;
      margin-right: auto;
      font-weight: 600;
      font-size: 1rem;
      color: var(--card-background-color);
    }
  
    ul {
      list-style-type: none;
      text-align: center;
      padding: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 3em;
      height: 4em;
      margin: 0 auto;
      flex-grow: 1;
      font-size: 0.8rem;
    }
  
    nav ul li a {
      text-decoration: none;
      color: var(--card-background-color);
    }
  
    .font-white {
      color: var(--card-background-color);
    }
  
    .scrolled {
      box-shadow: 0 4px 30px rgba(3, 3, 3, 0.1);
      backdrop-filter: blur(6.9px);
      -webkit-backdrop-filter: blur(6.9px);
      border: 1px solid rgba(27, 27, 27, 0.52);
    }
    
    .active-link {
        text-decoration: underline;
        text-underline-offset: 0.5em;
    }
</style>
