<script>
    import data from "../assets/animaldata.js";
    import Card from "../components/Card.svelte";
</script>

<div id="wrapper">
    <header>
        <nav></nav>
    </header>
    <main>
        {#each data as animal}
            <Card {animal} />
        {/each}
    </main>
</div>

<style>
    #wrapper {
        display: grid;
        grid-template-rows: auto auto 1fr;
        margin: auto;
    }

    main {
        display: flex;
        justify-content: center;
        flex-wrap: wrap;
        gap: 1em;
        padding: 2em;
    }
</style>
