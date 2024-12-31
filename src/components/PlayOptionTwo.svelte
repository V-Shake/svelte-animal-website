<script>
    import { IconSwords } from "@tabler/icons-svelte";
    import { IconUser } from "@tabler/icons-svelte";
    import { IconDeviceImac } from "@tabler/icons-svelte";
</script>

<div>
    <main>
        <div class="rectangle">
            <div class="header">Duel Mode</div>
            <div
                style="display: flex; justify-content: space-between; width: 100%; padding: 40px 30px; color: var(--card-dark-color);"
            >
                <IconUser stroke={1} size={60} />
                <IconSwords stroke={1} size={60} />
                <IconDeviceImac stroke={1} size={60} />
            </div>
            <div class="subtitle">play with computer</div>
            <div class="thick-line"></div>
            <div class="description">battle against AI programs</div>
        </div>
    </main>
</div>

<style>
    main {
        display: flex;
        flex-direction: column;
        justify-content: center; 
        align-items: flex-end;
        height: 90%;
    }

    .rectangle {
        position: relative;
        flex-grow: 0;
        min-width: 320px;
        min-height: 470px;
        background-color: var(--website-green-color);
        display: flex;
        flex-direction: column;
        align-items: flex;
        justify-content: center;
        padding: 1rem;
        box-sizing: border-box;
        border-radius: 15px;
        transition: all 0.3s ease;
        cursor: pointer;
    }

    .rectangle:hover {
        transform: translateY(-10px);
        box-shadow: 0 6px 12px rgba(32, 255, 203, 0.1);
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