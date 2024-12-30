<script>
    import PlayOptionOne from "../components/PlayOptionOne.svelte";
    import { IconCards } from "@tabler/icons-svelte";
</script>

<main>
    <PlayOptionOne />
    <div class="centered-container">
        <div class="icon-cards-container">
            <IconCards stroke={1} size={120} />
        </div>
        <div class="game-rules">
            <p>Play Quartet Game</p>
        </div>
        <div class="additional-text">
            <p>Each player holds only the top card visible, with the rest hidden. On their turn, they choose a value from their top card, aiming to beat the opponent’s card. If they win, they take the opponent’s card. For example, in a "max speed" round, the player with the higher speed wins the card.</p>
            <p>The game ends when a player runs out of cards, and the one with the most cards wins.</p>
        </div>
        
    </div>
</main>

<style>
    main {
        position: relative; /* Needed for positioning child elements absolutely */
    }

    .centered-container {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        text-align: center;
        display: flex;
        flex-direction: column; /* Ensures vertical stacking of children */
        align-items: center; /* Centers content horizontally */
        gap: 0.5rem; /* Adds space between elements */
    }

    .icon-cards-container {
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .game-rules {
        margin-top: 2rem;
        font-size: 1rem;
        padding: 0 5rem;
        border: 1px solid var(--card-background-color);
        border-radius: 10px;
    }

    .additional-text {
        font-size: 0.8rem;
        width: 22rem;
        
    }
</style>
