<script>
  import Router from "svelte-spa-router";
  import LandingPage from "./pages/LandingPage.svelte";
  import CardOverview from "./pages/CardOverview.svelte";
  import Quiz from "./pages/Quiz.svelte";
  import Navbar from "./components/Navbar.svelte";
  import "../public/fonts/stylesheet.css";
  // import Footer from "./components/Footer.svelte";

  const routes = {
    "/": LandingPage,
    "/cards": CardOverview,
    "/quiz": Quiz,
  };
</script>

<Navbar />
<!-- <Footer/> -->

<main>
  <div class="content-wrapper">
    <Router {routes} />
    <!-- The Router will render the appropriate component based on the URL -->
  </div>
</main>

<footer></footer>

<style>
</style>
