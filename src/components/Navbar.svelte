<script>
  import { onMount, onDestroy } from "svelte";

  // Scroll listener to detect scroll position
  let isScrolled = false;

  const handleScroll = () => {
    isScrolled = window.scrollY > 0; // Tracks scroll position
  };

  // Variable to track the current active route
  let currentRoute = window.location.hash || "#/";

  // Watch for changes in URL
  const updateActiveRoute = () => {
    currentRoute = window.location.hash || "#/";
  };

  onMount(() => {
    window.addEventListener("scroll", handleScroll);
    window.addEventListener("hashchange", updateActiveRoute);

    // Clean up event listeners on component unmount
    return () => {
      window.removeEventListener("scroll", handleScroll);
      window.removeEventListener("hashchange", updateActiveRoute);
    };
  });
</script>

<nav class={isScrolled ? "scrolled" : ""}>
  <div id="logo">
    <img src="/images/logo.svg" alt="My Logo" />
  </div>
  <ul>
    <li>
      <a href="#/" class:active-link={currentRoute === '#/'}>
        Home
      </a>
    </li>
    <li>
      <a href="#/cards" class:active-link={currentRoute === '#/cards'}>
        Card Gallery
      </a>
    </li>
    <li>
      <a href="#/quartet" class:active-link={currentRoute === '#/quartet'}>
        Quartet Game
      </a>
    </li>
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
    height: 5em; /* Set height for the navbar */
    color: var(--card-background-color);
  }

  #logo {
    display: flex;
    align-items: left;
    width: 5em;
  }

  ul {
    list-style-type: none;
    text-align: center;
    padding: 0;
    display: flex;
    justify-content: flex-end; /* Align items to the right */
    align-items: center;
    gap: 3em;
    height: 4em;
    margin: 0;
    flex-grow: 1;
    font-size: 0.8rem;
  }

  nav ul li a {
    text-decoration: none;
    color: var(--card-background-color);
    position: relative;
  }

  .scrolled {
    box-shadow: 0 4px 30px rgba(3, 3, 3, 0.1);
    backdrop-filter: blur(6.9px);
    -webkit-backdrop-filter: blur(6.9px);
    border: 1px solid rgba(27, 27, 27, 0.52);
  }

  .active-link::after {
    content: "";
    position: absolute;
    bottom: -0.4em; /* Adjust as needed */
    right: 0; /* Start from the right */
    width: 66%; /* Two-thirds of the word length */
    height: 2px; /* Adjust thickness as needed */
    background-color: var(--website-green-color);
    transform: translateX(-20%); /* Shift to the left */
    animation: underline 1s ease forwards;
  }

  @keyframes underline {
    from {
      width: 0;
    }
    to {
      width: 66%; /* Two-thirds of the word length */
    }
  }
</style>