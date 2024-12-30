<script>
    import PlayOptionOne from "../components/PlayOptionOne.svelte";
    import { IconCardsFilled } from "@tabler/icons-svelte";
</script>

<main>
    <PlayOptionOne />
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
        border-radius: 50%;
        padding: 1rem;
        color: var(--website-green-color);
    }

    .game-rules-container {
        width: 25rem;
        height: auto;
        padding: 1rem;
        position: relative;
        z-index: 1; /* Ensure the content stays on top */
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
        width: 300px; /* Size of the circle */
        height: 300px; /* Size of the circle */
        background-color: var(--website-dark-green-color);
        border-radius: 50%;
        z-index: -10; /* Ensure the circle is behind the content */
        backdrop-filter: blur(5px); /* Apply blur for glassmorphism effect */
    }

    .border-circle-background{
        position: absolute;
        top: 17%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 180px; /* Size of the circle */
        height: 180px; /* Size of the circle */
        border: 1px solid var(--website-green-color);
        opacity: 0.2;
        border-radius: 50%;
        z-index: -11; /* Ensure the circle is behind the content */
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
        margin: 1rem auto; /* Center the thick line */
    }
</style>
