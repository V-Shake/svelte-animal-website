<script>
    import { IconLogout2, IconHelp } from "@tabler/icons-svelte";
</script>

<div class="nav-container">
    <a href="#/quartet" class="back-button">
        <div class="icon-container">
            <IconLogout2 stroke={1} size={24} />
        </div>
        <span>quit</span>
    </a>
    <div class="help-button">
        <div class="icon-container">
            <IconHelp stroke={1} size={24} />
        </div>
        <span>help</span>
    </div>
</div>


<style>
    .nav-container {
        display: flex;
        flex-direction: row;
        align-items: flex-start;
        justify-content: flex-start;
        height: 100vh;
        padding: 1rem 6.5rem;
        gap: 2rem;
    }

    .back-button {
        display: flex;
        align-items: center;
        color: var(--card-background-color);
        cursor: pointer;
        transition: all 0.3s ease;
        margin-bottom: 2rem;
        text-decoration: none;
        font-size: 0.8rem;
    }

    .back-button:hover {
        color: var(
            --website-green-color
        ); /* Change background color on hover */
    }

    .icon-container {
        display: flex;
        align-items: center;
        justify-content: center;
        margin-right: 0.3rem; /* Space between icon and text */
    }

    .help-button {
        display: flex;
        align-items: center;
        color: var(--card-background-color);
        transition: all 0.3s ease;
        margin-bottom: 2rem;
        font-size: 0.8rem;
    }

    .help-button:hover {
        color: var(--website-green-color); /* Change background color on hover */
    }

</style>
