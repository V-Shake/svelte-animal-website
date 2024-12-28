<script>
    import { createEventDispatcher } from "svelte";

    const dispatch = createEventDispatcher();
    export let activeButton = "group"; // Default active sort option

    const options = [
        { id: "sort-groups", label: "Gruppen", sortKey: "group" },
        { id: "sort-max_weight", label: "Max Gewicht", sortKey: "max_weight" },
        { id: "sort-max_length", label: "Max Länge", sortKey: "max_length" },
        { id: "sort-max_age", label: "Max Alter", sortKey: "max_age" },
        { id: "sort-top_speed", label: "Max Geschwindigkeit", sortKey: "top_speed" },
        { id: "sort-litter_size", label: "Wurfgröße", sortKey: "litter_size" },
        { id: "sort-deaths", label: "Tödliche Vorfälle", sortKey: "deaths" },
        { id: "sort-intelligence", label: "Intelligenz", sortKey: "intelligence" }
    ];

    let dropdownVisible = false; // Controls visibility of the dropdown

    function handleSort(event) {
        const sortBy = event.target.dataset.sortkey; // Use dataset for sortKey
        activeButton = sortBy;
        dispatch("sort", sortBy);
    }

    function toggleDropdown() {
        dropdownVisible = !dropdownVisible; // Toggle dropdown visibility on button click
    }
</script>

<div class="select">
    <div class="selected" tabindex="0" on:click={toggleDropdown}>
        <span>Sort by</span>
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="arrow" class:rotate={dropdownVisible}>
            <path d="M6 9l6 6l6-6" />
        </svg>
    </div>
    
    {#if dropdownVisible}
        <div class="options">
            {#each options as option}
                <div 
                    class="option {activeButton === option.sortKey ? 'active' : ''}" 
                    on:click={handleSort}
                    data-sortkey={option.sortKey}>
                    {option.label}
                </div>
            {/each}
        </div>
    {/if}
</div>

<style>
    /* Dropdown container */
    .select {
        position: relative;
        display: inline-block;
        font-family: Arial, sans-serif;
        width: 100%; /* Allow to stretch horizontally */
        margin: 1rem; 
    }

    /* Selected button style */
    .selected {
        display: flex;
        justify-content: space-between;
        align-items: center;
        background-color: #060606;
        color: var(--card-background-color);
        padding: 0.8rem 1rem;
        border-radius: 5px;
        cursor: pointer;
        transition: all 0.3s ease;
        width: fit-content; /* To adjust width based on content */
    }

    .selected:hover {
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
    }

    .arrow {
        margin-left: 0.5rem;
        transition: transform 0.3s ease;
    }

    /* Rotate the arrow when dropdown is visible */
    .rotate {
        transform: rotate(180deg); /* Rotate arrow when dropdown is visible */
    }

    /* Options container (horizontal layout) */
    .options {
        display: flex;
        justify-content: left; /* Center the options */
        gap: 1rem; /* Add gaps between the options */
        margin-top: 0.5rem; /* Adds space between the button and options */
        background-color: #060606;
        border-radius: 5px;
        overflow: hidden;
        font-size: 0.8rem;
        white-space: nowrap; /* Prevent wrapping */
        width: 100%; /* Ensure the dropdown takes up the full width */
    }

    /* Option item */
    .option {
        padding: 0.5rem 2rem;
        color: var(--card-background-color);
        cursor: pointer;
        transition: background-color 0.3s ease, color 0.3s ease;
        display: inline-block;
        white-space: nowrap; /* Prevent text from wrapping */
        margin: 0.5rem;
    }

    .option:hover {
        background-color: var(--card-background-color);
        color: var(--card-dark-color);
        border-radius: 5px;
    }

    /* Active state for the selected option */
    .option.active {
        background-color: var(--card-background-color);
        color: var(--card-dark-color);
        border-radius: 5px;
    }
</style>
