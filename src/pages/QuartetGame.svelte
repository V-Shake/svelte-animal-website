<script>
    import { IconArrowLeft } from "@tabler/icons-svelte";
</script>

<main class="main-container">
    <a href="#/quartet" class="back-button">
        <IconArrowLeft stroke={1} size={24} />
        Back to Overview
    </a>
    <div class="content">
        <!-- Add your QuartetGame content here -->
        <h1>Quartet Game</h1>
        <p>Welcome to the Quartet Game page!</p>
    </div>
</main>

<style>
    .main-container {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        height: 100vh;
        padding: 2rem;
    }

    .back-button {
        display: flex;
        align-items: center;
        background-color: var(--website-green-color); /* Ensure the background color is set */
        color: var(--card-background-color); /* Ensure the text color contrasts with the background */
        border: none;
        padding: 0.5rem 1rem;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s ease;
        margin-bottom: 2rem;
        text-decoration: none; /* Remove underline from link */
    }

    .back-button:hover {
        background-color: var(--website-dark-green-color); /* Change background color on hover */
    }


    .content {
        text-align: center;
    }
</style>