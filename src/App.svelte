<script>
  import Router from "svelte-spa-router";
  import LandingPage from "./pages/LandingPage.svelte";
  import CardOverview from "./pages/CardOverview.svelte";
  import "../public/fonts/stylesheet.css";

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

</style>
