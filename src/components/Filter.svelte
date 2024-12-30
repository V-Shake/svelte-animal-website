<script>
    import { createEventDispatcher } from "svelte";
    import { IconAdjustmentsHorizontal } from "@tabler/icons-svelte";

    const dispatch = createEventDispatcher();
    export let activeButton = "group"; // Default active sort option
    export let activeFilter = "all"; 

    const sortOptions = [
        { "id": "sort-groups", "label": "Groups", "sortKey": "group" },
        { "id": "sort-max_weight", "label": "Max Weight", "sortKey": "max_weight" },
        { "id": "sort-max_length", "label": "Max Length", "sortKey": "max_length" },
        { "id": "sort-max_age", "label": "Max Age", "sortKey": "max_age" },
        { "id": "sort-top_speed", "label": "Max Speed", "sortKey": "top_speed" },
        { "id": "sort-litter_size", "label": "Litter Size", "sortKey": "litter_size" },
        { "id": "sort-deaths", "label": "Fatal Incidents", "sortKey": "deaths" },
        { "id": "sort-intelligence", "label": "Intelligence", "sortKey": "intelligence" }
    ];

    const filterOptions = [
        { id: "filter-all", label: "All", filterKey: "all" },
        { id: "filter-predators", label: "Predators", filterKey: "Predators" },
        { id: "filter-poisonous", label: "Poisonous and Infectious", filterKey: "Poisonous and Infectious" },
        { id: "filter-reptiles", label: "Reptiles", filterKey: "Reptiles" },
        { id: "filter-sea_creatures", label: "Sea Creatures", filterKey: "Sea Creatures" },
        { id: "filter-marine_giants", label: "Marine Giants", filterKey: "Marine Giants" },
        { id: "filter-large_mammals", label: "Large Mammals", filterKey: "Large Mammals" },
        { id: "filter-land_mammals", label: "Land Mammals", filterKey: "Land Mammals" },
        { id: "filter-birds", label: "Birds", filterKey: "Birds" }
    ];


    let dropdownVisible = false; // Controls visibility of the dropdown

    function handleSort(event) {
        const sortBy = event.target.dataset.sortkey; // Use dataset for sortKey
        activeButton = sortBy;
        dispatch("sort", sortBy);
    }

    function handleFilter(event) {
        const filterBy = event.target.dataset.filterkey; // Use dataset for filterKey
        activeFilter = filterBy;
        dispatch("filter", filterBy);
    }

    function toggleDropdown() {
        dropdownVisible = !dropdownVisible; // Toggle dropdown visibility on button click
    }
</script>

<div class="select">
    <div class="selected" tabindex="0" on:click={toggleDropdown}>
        <IconAdjustmentsHorizontal stroke={1.5} style="margin-right: 0.3rem; width: 20px; height: 20px;" />
        <span>filter</span>
        <svg
            xmlns="http://www.w3.org/2000/svg"
            width="20"
            height="20"
            fill="none"
            stroke="currentColor"
            stroke-width="1.5"
            stroke-linecap="round"
            stroke-linejoin="round"
            class="arrow"
            class:rotate={dropdownVisible}
        >
            <path d="M6 9l6 6l6-6" />
        </svg>
        </div>

        <div class="options {dropdownVisible ? 'visible' : ''}">
            <div class="sort-by">Sort by</div>
            <div class="option-container">
                {#each sortOptions as option}
                    <div 
                        class="option {activeButton === option.sortKey ? 'active' : ''}" 
                        on:click={handleSort}
                        data-sortkey={option.sortKey}>
                        {option.label}
                    </div>
                {/each}
            </div>
            <div class="filter-by">Filter by</div>
            <div class="option-container">
                {#each filterOptions as option}
                    <div 
                        class="option {activeFilter === option.filterKey ? 'active' : ''}" 
                        on:click={handleFilter}
                        data-filterkey={option.filterKey}>
                        {option.label}
                    </div>
                {/each}
            </div>
        </div>
    </div>

<style>
    /* Dropdown container */
    .select {
        position: relative;
        display: inline-block;
        width: 90%; /* Allow to stretch horizontally */
    }

    /* Selected button style */
    .selected {
        display: flex;
        justify-content: space-between;
        align-items: center;
        background-color: var(--dark-corlor);
        color: var(--card-background-color);
        padding: 0.8rem 1rem;
        border-radius: 10px;
        cursor: pointer;
        transition: all 0.3s ease;
        width: fit-content; /* To adjust width based on content */
        font-size: 0.8rem;
        line-height: 1.2;
    }

    .selected:hover {
        color: var(--website-green-color);
    }

    .arrow {
        margin-left: 0.6rem;
        transition: transform 0.3s ease;
    }

    /* Rotate the arrow when dropdown is visible */
    .rotate {
        transform: rotate(180deg); /* Rotate arrow when dropdown is visible */
    }

    /* Options container (horizontal layout) */
    .options {
        display: flex;
        flex-direction: column;
        justify-content: left; /* Center the options */
        margin-top: 0.5rem; /* Adds space between the button and options */
        background-color: var(--dark-corlor);
        border-radius: 10px;
        overflow: hidden;
        font-size: 0.7rem;
        white-space: nowrap; /* Prevent wrapping */
        width: 100%; /* Ensure the dropdown takes up the full width */
        opacity: 0;
        transform: translateY(-10px);
        transition: all 0.3s ease;
        max-height: 0;
    }

    .options.visible {
        opacity: 1;
        transform: translateY(0);
        max-height: 500px; /* Adjust as needed */
    }

    .sort-by, .filter-by {
        padding: 0 1.5rem;
        display: flex;
        align-items: left;
        margin-top: 0.5rem;
        color: var(--card-background-color);
        width: 100%;
    }

    /* Option container for horizontal layout */
    .option-container {
        display: flex;
        align-items: left;
        flex-wrap: wrap; /* Allow options to wrap to the next line if needed */
        gap: 0.5rem; /* Add gaps between the options */
        padding: 0 1.5rem;
        margin-top: 0.5rem;
        margin-bottom: 1rem;
    }

    /* Option item */
    .option {
        padding: 0.4rem 1.1rem;
        color: var(--card-background-color);
        background-color: var(--card-dark-color);
        cursor: pointer;
        transition:
            background-color 0.3s ease,
            color 0.3s ease;
        display: inline-block;
        white-space: nowrap; /* Prevent text from wrapping */
        border-radius: 5px;
    }

    .option:hover {
        background-color:rgba(255, 255, 255, 0.05);
        color: var(--website-green-color);
        border-radius: 5px;
    }

    /* Active state for the selected option */
    .option.active {
        background-color: var(--website-dark-green-color);      
        color: var(--website-green-color);
        border-radius: 5px;
    }
</style>
