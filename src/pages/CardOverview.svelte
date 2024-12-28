<script>
    import data from "../assets/animaldata.js";
    import Card from "../components/Card.svelte";
    import Sort from "../components/Sort.svelte";

    let sortedData = data;
    let activeButton = "group"; // Ensure this matches default key in Sort.svelte

    function handleSort(event) {
        activeButton = event.detail; // Update the active button state
        sortedData = [...data].sort((a, b) => {
            if (activeButton === "group") return a.group.localeCompare(b.group);
            if (activeButton === "intelligence")
                return b.intelligence - a.intelligence;
            return parseFloat(b[activeButton]) - parseFloat(a[activeButton]);
        });
    }
</script>

<div id="wrapper">
    <header>
        <nav></nav>
    </header>
    <main>
        <Sort {activeButton} on:sort={handleSort} />
        {#each sortedData as animal}
            <Card {animal} highlight={activeButton} />
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
