<script>
    import data from "../assets/animaldata.js";
    import Card from "../components/Card.svelte";
    import Sort from "../components/Sort.svelte";

    let sortedData = data;
    let highlight = "";
    let activeButton = "group"; // Initialize with default

    function handleSort(event) {
        const sortBy = event.detail;
        activeButton = sortBy; // Update active button
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
        <Sort {activeButton} on:sort={handleSort} />
        <div id="cards-container">
            {#each sortedData as animal}
                <Card {animal} {highlight} />
            {/each}
        </div>
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
        flex-direction: column;
        align-items: center;
        gap: 1em;
        padding: 2em;
    }

    #cards-container {
        display: flex;
        justify-content: center;
        flex-wrap: wrap;
        gap: 1em;
        width: 100%;
    }

/* 
    #cards-container {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        gap: 1rem;
        justify-content: center;
        align-items: start;
        justify-items: center;
        width: 100%;
        max-width: 1200px;
        margin: 0 auto;
        padding: 1rem;
        background-color: var(--card-dark-color);
    } */
</style>
