<script>
  import data from "../assets/animaldata.js";
  import Card from "../components/Card.svelte";
</script>

<div id="wrapper">
    <header>
        <h1>Tierquartett</h1>
        <nav>
        </nav>
    </header>
    <main>
        {#each data as animal}
            <Card animal={animal}/>
        {/each}

    </main>
</div>

<style></style>