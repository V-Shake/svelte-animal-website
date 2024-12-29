<script>
</script>

<div>
    <main>
        <div class="rectangle">
            <div class="header">1 vs 1</div>
            <div class="subtitle">Play with other Players</div>
            <div class="thick-line"></div>
            <div class="description">random match or friend match</div>
        </div>
    </main>
</div>

<style>
    main {
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 2em;
    }

    .rectangle {
        width: 320px;
        height: 470px;
        border: 1px solid #000;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        padding: 20px;
        box-sizing: border-box;
    }

    .header {
        font-size: 24px;
        font-weight: bold;
        margin-bottom: 10px;
    }

    .subtitle {
        font-size: 18px;
        margin-bottom: 20px;
    }

    .thick-line {
        width: 100%;
        height: var(--thick-line-strength);
        background-color: var(--card-background-color);
        margin-bottom: 20px;
    }

    .description {
        font-size: 14px;
        text-align: center;
    }
</style>
