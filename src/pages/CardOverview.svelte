<script>
    import data from "../assets/animaldata.js";
    import Card from "../components/Card.svelte";
    import Sort from "../components/Sort.svelte";

    let sortedData = data;
    let highlight = "";

    function handleSort(event) {
        const sortBy = event.detail;
        highlight = sortBy;
        sortedData = [...data].sort((a, b) => {
            if (sortBy === "group") return a.group.localeCompare(b.group);
            if (sortBy === "intelligence")
                return b.intelligence - a.intelligence;
            return parseFloat(b[sortBy]) - parseFloat(a[sortBy]);
        });
    }
</script>

<div id="wrapper">
    <header>
        <nav></nav>
    </header>
    <main>
        <Sort on:sort={handleSort} />
        {#each sortedData as animal}
            <Card {animal} {highlight} />
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
