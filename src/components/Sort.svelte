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

    function handleSort(event) {
        const sortBy = event.target.value;
        dispatch("sort", sortBy);
    }
</script>

<div class="select">
    <div class="selected" tabindex="0">
        <span>Sort by: {options.find(option => option.sortKey === activeButton)?.label}</span>
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="arrow">
            <path d="M6 9l6 6l6-6" />
        </svg>
    </div>
    <div class="options">
        {#each options as option}
            <div 
                class="option {activeButton === option.sortKey ? 'active' : ''}" 
                on:click={() => handleSort({ target: { value: option.sortKey } })}>
                {option.label}
            </div>
        {/each}
    </div>
</div>

<style>
    /* Dropdown container */
    .select {
        position: relative;
        display: inline-block;
        font-family: Arial, sans-serif;
        width: 100%; /* Allow to stretch horizontally */
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
        margin: 0 auto; /* Center the selected item horizontally */
    }

    .selected:hover {
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
    }

    .arrow {
        margin-left: 0.5rem;
        transition: transform 0.3s ease;
    }

    /* Options container (horizontal layout) */
    .options {
        display: none;
        position: absolute;
        top: 100%;
        left: 0;
        right: 0;
        background-color: #060606;
        border-radius: 5px;
        overflow: hidden;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
        z-index: 10;
        white-space: nowrap; /* Prevent wrapping */
        transition: opacity 0.3s ease, visibility 0.3s ease;
        width: 100%; /* Ensure the dropdown takes up the full width */
        padding: 0.5rem 0; /* Add some padding for space between options */
    }

    /* Show options on hover */
    .select:hover .options {
        display: flex; /* Use flexbox to display options horizontally */
        justify-content: center; /* Center the options */
        gap: 1rem; /* Add gaps between the options */
        opacity: 1;
        visibility: visible;
    }

    /* Option item */
    .option {
        padding: 0.8rem 1rem;
        color: var(--card-background-color);
        cursor: pointer;
        transition: background-color 0.3s ease, color 0.3s ease;
        display: inline-block;
        white-space: nowrap; /* Prevent text from wrapping */
    }

    .option:hover {
        background-color: var(--card-background-color);
        color: var(--card-dark-color);
    }

    /* Active state for the selected option */
    .option.active {
        background-color: var(--card-background-color);
        color: var(--card-dark-color);
    }

    /* Arrow rotation when options are visible */
    .select:hover .arrow {
        transform: rotate(180deg);
    }
</style>
