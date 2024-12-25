<script>
  import Router from "svelte-spa-router";
  import LandingPage from "./pages/LandingPage.svelte";
  import CardOverview from "./pages/CardOverview.svelte";

  const routes = {
    "/": LandingPage,
    "/cards": CardOverview,
  };
</script>

<header>
  <h1>wildlife</h1>
  <nav>
    <ul>
      <li><a href="#/">Home</a></li>
      <li><a href="#/cards">Quartet Cards</a></li>
    </ul>
  </nav>
</header>

<main>
  <Router {routes} />
</main>

<footer></footer>

<style>
  header {
  background-color: var(--card-dark-color);
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

nav ul {
  display: flex;
  gap: 1em;
  list-style: none;
  padding: 0;
}

nav ul li a {
  color: white;
  text-decoration: none;
  font-weight: 600;
}

nav ul li a:hover {
  color: #7D982A;
}
</style>
