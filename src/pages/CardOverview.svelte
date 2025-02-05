<script>
    import data from "../assets/animaldata.js";
    import Card from "../components/Card.svelte";
    import Filter from "../components/Filter.svelte";
    import BackToTop from "../components/BackToTop.svelte";

    let sortedData = data;
    let filteredData = data;
    let highlight = "";
    let activeButton = "group"; // Initialize with default
    let activeFilter = "all"; // Initialize with no filter

    function handleSort(event) {
        const sortBy = event.detail;
        activeButton = sortBy; // Update active button
        highlight = sortBy;
        sortedData = [...filteredData].sort((a, b) => {
            if (sortBy === "group") return a.group.localeCompare(b.group);
            if (sortBy === "intelligence")
                return b.intelligence - a.intelligence;
            return parseFloat(b[sortBy]) - parseFloat(a[sortBy]);
        });
    }

    function handleFilter(event) {
        const filterBy = event.detail;
        activeFilter = filterBy; // Update active filter
        if (filterBy === "all") {
            filteredData = data;
        } else {
            filteredData = data.filter(
                (animal) => animal.groupname === filterBy,
            );
        }
        handleSort({ detail: activeButton }); // Reapply sorting on filtered data
    }
</script>

<div id="wrapper">
    <header>
        <nav>
        </nav>
    </header>
    <main>
        <Filter
            {activeButton}
            {activeFilter}
            on:sort={handleSort}
            on:filter={handleFilter}
        />
        <div id="cards-container">
            {#each sortedData as animal}
                <Card {animal} {highlight} />
            {/each}
        </div>
        <BackToTop /> 
    </main>
</div>

<style>
    #wrapper {
        width: 100%;
        height: 100;
        box-sizing: border-box;
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


    @media (max-width: 1024px) {
        #cards-container {
            grid-template-columns: repeat(3, 1fr);
        }
    }

    @media (max-width: 768px) {
        main {
            padding: 1em;
        }
        #cards-container {
            grid-template-columns: repeat(2, 1fr);
            gap: 0.5em;
        }
    }

    @media (max-width: 480px) {
        main {
            padding: 0.5em;
        }
        #cards-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 0.25em;
        }
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
