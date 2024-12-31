<script>
    import { IconSwords } from "@tabler/icons-svelte";
    import { IconUser } from "@tabler/icons-svelte";
    import { IconLock } from "@tabler/icons-svelte";
</script>

<div>
    <main>
        <div class="rectangle">
            <div class="icon-lock-container">
                <IconLock class="icon-lock" stroke={1} size={48} />
            </div>
            <div class="header">Multiplayer Mode</div>
            <div
                style="display: flex; justify-content: space-between; width: 100%; padding: 40px 30px; color: var(--card-dark-color);"
            >
                <IconUser stroke={1} size={60} />
                <IconSwords stroke={1} size={60} />
                <IconUser stroke={1} size={60} />
            </div>
            <div class="subtitle">play with other players</div>
            <div class="thick-line"></div>
            <div class="description">random match or friend match</div>
        </div>
    </main>
</div>

<style>
    main {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: flex-start;
        height: 90%; 
    }

    .rectangle {
        position: relative;
        flex-grow: 0;
        min-width: 320px;
        min-height: 470px;
        background-color: var(--website-dark-green-color);
        display: flex;
        flex-direction: column;
        align-items: flex;
        justify-content: center;
        padding: 1rem;
        box-sizing: border-box;
        border-radius: 15px;
        transition: all 0.3s ease;
    }

    .icon-lock-container {
        position: absolute;
        top: 1.5rem;
        left: 50%;
        transform: translateX(-50%); /* This centers the icon horizontally */
    }

    .rectangle:hover .icon-lock-container {
        color: var(--website-green-color);
    }

    .header {
        font-size: 24px;
        font-weight: bold;
        margin-bottom: 10px;
        color: var(--card-dark-color);
    }

    .subtitle {
        font-size: 18px;
        margin-bottom: 20px;
        color: var(--card-dark-color);
    }

    .thick-line {
        width: 100%;
        height: var(--thick-line-strength);
        background-color: var(--card-dark-color);
        margin-bottom: 20px;
    }

    .description {
        font-size: 14px;
        text-align: center;
        color: var(--card-dark-color);
    }
</style>
