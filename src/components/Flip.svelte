<script>
    import Card from './Card.svelte';
    export let animal;

    let flipped = false;

    function toggleFlip() {
        flipped = !flipped;
    }
</script>

<div class="flip-wrapper" on:click={toggleFlip}>
    <div class="flip-card {flipped ? 'flipped' : ''}">
        <div class="flip-card-front">
            <Card {animal} />
        </div>
        <div class="flip-card-back">
            <Card {animal} />
        </div>
    </div>
</div>

<style>
.flip-wrapper {
    perspective: 1000px;
    cursor: pointer;
}

.flip-card {
    width: 100%;
    height: 100%;
    transform-style: preserve-3d;
    transition: transform 0.6s ease;
    position: relative;
}

.flip-card.flipped {
    transform: rotateY(180deg);
}

.flip-card-front,
.flip-card-back {
    backface-visibility: hidden;
    position: absolute;
    width: 100%;
    height: 100%;
}

.flip-card-back {
    transform: rotateY(180deg);
}
</style>
