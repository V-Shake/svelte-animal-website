<script>
    import PlayOption from "../components/PlayOption.svelte";

    let selectedOption = null;

    function handleOptionSelect(option) {
        selectedOption = option;
    }
</script>

<main>
    <PlayOption on:select={handleOptionSelect} />

    {#if selectedOption}
        <p>You selected: {selectedOption}</p>
    {/if}
</main>

<style>
    main {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 1em;
        padding: 2em;
    }
</style>
