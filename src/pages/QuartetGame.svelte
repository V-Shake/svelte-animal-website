<script>
    import PlayOptionOne from "../components/PlayOptionOne.svelte";
    import PlayOptionTwo from "../components/PlayOptionTwo.svelte";
    import { IconCardsFilled } from "@tabler/icons-svelte";
</script>

<main class="main-container">
    <PlayOptionOne />
    
    <!-- Centered container between PlayOptionOne and PlayOptionTwo -->
    <div class="centered-container">
        <div class="circle-background"></div>
        <div class="border-circle-background"></div>
        <div class="icon-cards-container">
            <IconCardsFilled stroke={1} size={120} />
        </div>
        <div class="game-rules-container">
            <div class="game-rules">
                <p>Play Quartet Game</p>
            </div>
            <div class="thick-line"></div>
            <div class="additional-text">
                <p>
                    Each player holds only the top card visible, with the rest
                    hidden. On their turn, they choose a value from their top
                    card, aiming to beat the opponent’s card. If they win, they
                    take the opponent’s card. For example, in a "max speed"
                    round, the player with the higher speed wins the card.
                </p>
                <p>
                    The game ends when a player runs out of cards, and the one
                    with the most cards wins.
                </p>
            </div>
        </div>
    </div>
    
    <PlayOptionTwo />
</main>

<style>
    .main-container {
        display: flex;
        justify-content: space-between; /* Distribute the play options evenly */
        align-items: center; /* Align vertically in the center */
        height: 90vh; /* Full height */
        padding: 0 8rem; /* Add padding on both sides */
    }

    /* Centered container */
    .centered-container {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        position: relative;
        flex: 1;
        text-align: center;
    }

    .icon-cards-container {
        display: flex;
        justify-content: center;
        align-items: center;
        border-radius: 50%;
        padding: 1rem;
        color: var(--website-green-color);
    }

    .game-rules-container {
        width: 25rem;
        height: auto;
        padding: 1rem;
        position: relative;
        z-index: 1;
        box-shadow: 0 4px 30px rgba(3, 3, 3, 0.1);
        backdrop-filter: blur(6.9px);
        -webkit-backdrop-filter: blur(6.9px);
        border: 1px solid rgba(27, 27, 27, 0.52);
        border-radius: 15px;
    }

    /* Circle background */
    .circle-background {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 300px;
        height: 300px;
        background-color: var(--website-dark-green-color);
        border-radius: 50%;
        z-index: -10;
        backdrop-filter: blur(5px);
    }

    .border-circle-background {
        position: absolute;
        top: 15%;
        left: 43%;
        transform: translate(-50%, -50%);
        width: 120px;
        height: 120px;
        border: 1px solid var(--website-green-color);
        opacity: 0.2;
        border-radius: 50%;
        z-index: -11;
    }

    .game-rules {
        font-size: 1.2rem;
        font-weight: bold;
    }

    .additional-text {
        font-size: 0.8rem;
        padding: 0 1rem;
    }

    .thick-line {
        width: 90%;
        height: var(--thick-line-strength);
        background-color: rgba(255, 255, 255, 0.4);
        margin: 1rem auto;
    }
</style>
