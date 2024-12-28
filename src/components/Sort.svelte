<script>
    import { createEventDispatcher } from "svelte";
    
    const dispatch = createEventDispatcher();
    export let activeButton; // Receive the activeButton prop

    function setActiveButton(buttonId) {
        activeButton = buttonId;
    }

    function handleSort(sortBy) {
        setActiveButton(sortBy);
        dispatch("sort", sortBy);
    }
</script>

<div id="sorting-buttons">
    <span>Sortiere nach: </span>
    <button
        id="sort-groups"
        class:active={activeButton === "sort-groups"}
        on:click={() => handleSort("group")}>Gruppen</button>
    <button
        id="sort-max_weight"
        class:active={activeButton === "sort-max_weight"}
        on:click={() => handleSort("max_weight")}>Max Gewicht</button>
    <button
        id="sort-max_length"
        class:active={activeButton === "sort-max_length"}
        on:click={() => handleSort("max_length")}>Max Länge</button>
    <button
        id="sort-max_age"
        class:active={activeButton === "sort-max_age"}
        on:click={() => handleSort("max_age")}>Max Alter</button>
    <button
        id="sort-top_speed"
        class:active={activeButton === "sort-top_speed"}
        on:click={() => handleSort("top_speed")}>Max Geschwindigkeit</button>
    <button
        id="sort-litter_size"
        class:active={activeButton === "sort-litter_size"}
        on:click={() => handleSort("litter_size")}>Wurfgröße</button>
    <button
        id="sort-deaths"
        class:active={activeButton === "sort-deaths"}
        on:click={() => handleSort("deaths")}>Tödliche Vorfälle</button>
    <button
        id="sort-intelligence"
        class:active={activeButton === "sort-intelligence"}
        on:click={() => handleSort("intelligence")}>Intelligenz</button>
</div>

<style>
    /* Sorting Buttons */
    #sorting-buttons span {
        font-size: 1rem;
        margin-right: 2rem;
        display: inline-block;
        line-height: 2rem;
        grid-column: span 1;
        text-align: center;
    }

    #sorting-buttons {
        margin: 3rem;
        display: grid;
        grid-template-columns: repeat(9, 1fr);
        gap: 0.5rem;
        justify-content: center;
        align-items: center;
        padding: 0.1rem;
    }

    #sorting-buttons button {
        appearance: none;
        background-color: #060606;
        border: #030303;
        border-radius: 10px;
        box-sizing: border-box;
        color: var(--card-background-color);
        cursor: pointer;
        font-size: 0.7rem;
        line-height: normal;
        min-height: 20px;
        padding: 8px 10px;
        text-align: center;
        text-decoration: none;
        transition: all 300ms cubic-bezier(0.23, 1, 0.32, 1);
        user-select: none;
        -webkit-user-select: none;
        touch-action: manipulation;
        width: 100%;
        will-change: transform;
    }

    /* Hover Effect */
    #sorting-buttons button:hover {
        box-shadow: rgba(0, 0, 0, 0.25) 0 8px 15px;
        transform: translateY(-2px);
    }

    /* Active Button State */
    #sorting-buttons button.active {
        background-color: var(--card-background-color);
        color: var(--card-dark-color);
    }

    #sorting-buttons button:active {
        box-shadow: none;
        transform: translateY(0);
    }

    #sorting-buttons button:disabled {
        pointer-events: none;
    }
</style>