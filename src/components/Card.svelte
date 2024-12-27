<script>
    export let animal;
</script>

<div class="card-wrapper">
    <div class="card-content">
        <div class="card-header">
            <div class="card-number">{animal.group}{animal.group_number}</div>
            <div class="card-title">{animal.name_german}</div>
        </div>
        <img class="card-image" src={`public/images/${animal.group}${animal.group_number}.png`} alt={animal.name_german} />
        <div class="card-trivia">{animal.trivia_german}</div>
        <div class="card-statistics">
            <div class="statistics-pair">
                <div class="stat-name">
                    <img src="images/icons/weight.svg" alt="weight" />
                </div>
                <div class="stat-value">{animal.max_weight}</div>
            </div>
            <div class="statistics-pair">
                <div class="stat-name">
                    <img src="images/icons/length.svg" alt="length" />
                </div>
                <div class="stat-value">{animal.max_length}</div>
            </div>
            <div class="statistics-pair">
                <div class="stat-name">
                    <img src="images/icons/age.svg" alt="max age" />
                </div>
                <div class="stat-value">{animal.max_age}</div>
            </div>
            <div class="statistics-pair">
                <div class="stat-name">
                    <img src="images/icons/speed.svg" alt="max speed" />
                </div>
                <div class="stat-value">{animal.top_speed}</div>
            </div>
            <div class="statistics-pair">
                <div class="stat-name">
                    <img src="images/icons/offspring.svg" alt="offspring per cycle" />
                </div>
                <div class="stat-value">{animal.litter_size}</div>
            </div>
            <div class="statistics-pair">
                <div class="stat-name">
                    <img src="images/icons/death.svg" alt="caused yearly human casualties" />
                </div>
                <div class="stat-value">{animal.deaths}</div>
            </div>
        </div>
    </div>
</div>

<style>
    :root {
        --dark-color: #181818;
        --background-color: beige;
    }
    
    .card-wrapper {
        font-family: "Inter", sans-serif;
        background-color: var(--dark-color);
        padding: 10px;
        display: inline-block;
    }
    
    .card-content {
        width: 300px;
        height: 450px;
        display: grid;
        grid-template-rows: repeat(10, 1fr);
        border-radius: 10px;
        background-color: var(--background-color);
        overflow: hidden;
    }
    
    .card-header {
        display: grid;
        grid-template-columns: 1fr 3fr;
        border-bottom: 2px solid var(--dark-color);
    }
    
    .card-number {
        font-weight: 600;
        color: white;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 1.5rem;
        background-color: limegreen;
    }
    
    .card-title {
        text-transform: uppercase;
        font-weight: 600;
        color: var(--dark-color);
        display: flex;
        align-items: center;
        padding-left: 0.5rem;
        font-size: 1.2rem;
    }
    
    .card-image {
        object-fit: cover;
        width: 100%;
        max-width: 100%;
        max-height: 100%;
        grid-row: span 5;
    }
    
    .card-trivia {
        background-color: cadetblue;
        font-style: italic;
        font-size: .8rem;
        text-align: center;
        padding: 0 1em;
        display: flex;
        align-items: center;
        border-bottom: 1px solid var(--dark-color);
        border-top: 1px solid var(--dark-color);
    }
    
    .card-statistics {
        grid-row: span 3;
        display: grid;
        grid-template-columns: 1fr 1fr;
        grid-template-rows: repeat(3, 1fr);
        background-color: var(--dark-color);
        gap: 2px;
    }
    
    .statistics-pair {
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        background-color: var(--background-color);
    }
    
    .stat-name {
        background-color: burlywood;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    
    .stat-name img {
        max-height: 50%;
        max-width: 50%;
        object-fit: contain;
    }
    
    .stat-value {
        display: flex;
        align-items: center;
        justify-content: center;
    }

</style>
