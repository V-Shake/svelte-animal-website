<script>
  import { onMount } from 'svelte';
  import { IconMenu2 } from '@tabler/icons-svelte';

  let isScrolled = false;
  let isMenuOpen = false;
  let currentRoute = window.location.hash || '#/';

  const handleScroll = () => {
    isScrolled = window.scrollY > 0;
  };

  const updateActiveRoute = () => {
    currentRoute = window.location.hash || '#/';
  };

  onMount(() => {
    window.addEventListener('scroll', handleScroll);
    window.addEventListener('hashchange', updateActiveRoute);

    return () => {
      window.removeEventListener('scroll', handleScroll);
      window.removeEventListener('hashchange', updateActiveRoute);
    };
  });

  function navigateHome() {
    window.location.hash = '#/';
  }

  function toggleMenu() {
    isMenuOpen = !isMenuOpen;
  }
</script>

<nav class={isScrolled ? 'scrolled' : ''}>
  <div id="logo" on:click={navigateHome} style="cursor: pointer;">
    <img src="/images/logo.svg" alt="My Logo" />
  </div>

  <div class="burger-menu" on:click={toggleMenu}>
    <IconMenu2 size={24} />
  </div>

  <!-- Mobile Menu with Background Box -->
  <div class="menu-container" class:open={isMenuOpen}>
    <ul>
      <li>
        <a href="#/" class:active-link={currentRoute === '#/'}>Home</a>
      </li>
      <li>
        <a href="#/cards" class:active-link={currentRoute === '#/cards'}>Card Gallery</a>
      </li>
      <li>
        <a href="#/quartet" class:active-link={currentRoute === '#/quartet'}>Quartet Game</a>
      </li>
      <li>
        <a href="#/quiz" class:active-link={currentRoute === '#/quiz'}>Quiz</a>
      </li>
    </ul>
  </div>
</nav>

<style>
  nav {
    background-color: rgba(14, 14, 14, 0.93);
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    position: fixed;
    top: 0;
    z-index: 1000;
    padding: 0.6rem 6.5rem;
    transition: background-color 0.3s ease;
    height: 5em;
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
    justify-content: flex-end;
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
    content: '';
    position: absolute;
    bottom: -0.4em;
    right: 0;
    width: 66%;
    height: 2px;
    background-color: var(--website-green-color);
    transform: translateX(-20%);
    animation: underline 1s ease forwards;
  }

  @keyframes underline {
    from {
      width: 0;
    }
    to {
      width: 66%;
    }
  }

  .burger-menu {
    display: none;
    cursor: pointer;
  }

  @media (max-width: 480px) {
    nav {
      padding: 0.6rem 3rem;
      position: fixed;
      width: 100%;
      left:0;
    }

    .burger-menu {
      display: block;
      z-index: 1100; /* Ensure it stays above */
    }

    /* Background box for menu */
    .menu-container {
      display: none;
      position: absolute;
      top: 4rem; /* Below navbar */
      left: 0;
      width: 100%; /* Full width */
      height: 8rem;
      background-color: rgba(14, 14, 14);
      padding: 7rem;
      border-radius: 8px;
      box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.3);
      animation: fadeIn 0.3s ease-in-out;
    }

    /* Show when menu is open */
    .menu-container.open {
      display: block;
    }

    /* Align text inside menu & fix gaps */
    .menu-container ul {
      display: flex;
      flex-direction: column;
      align-items: flex-start;
      padding: 0;
      margin: 0;
      gap: 0.5rem;
    }

    /* Reduce gaps */
    .menu-container li {
      width: 100%;
      padding: 0.3rem 0;
      margin: 0;
    }

    .menu-container a {
      display: block;
      width: 100%;
      text-decoration: none;
      color: white;
      padding: 0.3rem 0;
    }

    @keyframes fadeIn {
      from {
        opacity: 0;
        transform: translateY(-10px);
      }
      to {
        opacity: 1;
        transform: translateY(0);
      }
    }
  }
</style>
