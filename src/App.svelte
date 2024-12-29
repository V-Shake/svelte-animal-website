<script>
  import Router from "svelte-spa-router";
  import LandingPage from "./pages/LandingPage.svelte";
  import CardOverview from "./pages/CardOverview.svelte";
  import Navbar from "./components/Navbar.svelte"; // Import the Nav component
  import "../public/fonts/stylesheet.css";

  const routes = {
    "/": LandingPage,
    "/cards": CardOverview,
  };
</script>

<Navbar />

<main>
  <div class="content-wrapper">
    <Router {routes} />
    <!-- The Router will render the appropriate component based on the URL -->
  </div>
</main>

<footer></footer>

<style>
</style>
