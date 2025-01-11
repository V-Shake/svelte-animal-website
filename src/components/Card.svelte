<script>
    export let animal;
    export let highlight = '';

    let flipped = false;

    function toggleFlip() {
        flipped = !flipped;
    }
</script>

<div class="card-wrapper {`group-${animal.group}`}" on:click={toggleFlip}>
    <div class="card {flipped ? 'flipped' : ''}">
        <div class="card-front {`group-${animal.group}`}">
            <img
                class="card-image border-bottom"
                src={`public/images/${animal.group}${animal.group_number}.png`}
                alt={animal.name}
            />
            <div class="card-number">{animal.group}{animal.group_number}</div>
            <div class="card-title">{animal.name}</div>
            <div class="card-trivia border-bottom">{animal.trivia}</div>
            <div class="group-rectangle">{animal.group}</div>
            <div class="groupname">{animal.groupname}</div>
            <div class="intelligence-box">
                <div class="intelligence-icon">
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        width="24"
                        height="24"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        class="icon icon-tabler icons-tabler-outline icon-tabler-bulb"
                    >
                        <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                        <path
                            d="M3 12h1m8 -9v1m8 8h1m-15.4 -6.4l.7 .7m12.1 -.7l-.7 .7"
                        />
                        <path
                            d="M9 16a5 5 0 1 1 6 0a3.5 3.5 0 0 0 -1 3a2 2 0 0 1 -4 0a3.5 3.5 0 0 0 -1 -3"
                        />
                        <path d="M9.7 17l4.6 0" />
                    </svg>
                </div>
                <div class="intelligence-content">{animal.intelligence}</div>
            </div>
            <div class="stat-box">
                <div class="stat-pair">
                    <div class="stat-icon">
                        <img src="images/icons/weight.svg" alt="weight" />
                    </div>
                    <div class="stat-content {highlight === 'max_weight' ? 'highlight' : ''}">{animal.max_weight}</div>
                </div>
                <div class="stat-pair">
                    <div class="stat-icon">
                        <img src="images/icons/length.svg" alt="length" />
                    </div>
                    <div class="stat-content {highlight === 'max_length' ? 'highlight' : ''}">{animal.max_length}</div>
                </div>
                <div class="stat-pair">
                    <div class="stat-icon">
                        <img src="images/icons/age.svg" alt="max age" />
                    </div>
                    <div class="stat-content {highlight === 'max_age' ? 'highlight' : ''}">{animal.max_age}</div>
                </div>
                <div class="stat-pair">
                    <div class="stat-icon">
                        <img src="images/icons/speed.svg" alt="max speed" />
                    </div>
                    <div class="stat-content {highlight === 'top_speed' ? 'highlight' : ''}">{animal.top_speed}</div>
                </div>
                <div class="stat-pair">
                    <div class="stat-icon">
                        <img
                            src="images/icons/offspring.svg"
                            alt="offspring per cycle"
                        />
                    </div>
                    <div class="stat-content {highlight === 'litter_size' ? 'highlight' : ''}">{animal.litter_size}</div>
                </div>
                <div class="stat-pair">
                    <div class="stat-icon">
                        <img
                            src="images/icons/death.svg"
                            alt="caused yearly human casualties"
                        />
                    </div>
                    <div class="stat-content {highlight === 'deaths' ? 'highlight' : ''}">{animal.deaths}</div>
                </div>
            </div>
            <div class="card-bottom">{animal.continents}</div>
        </div>
        <div class="card-back">
            <div class="card-back-header border-bottom"></div>
            <div class="card-back-title">{animal.name}</div>
            <div class="card-back-number">
                {animal.group}{animal.group_number}
            </div>
            <div class="card-back-content">
                <ul class="funfact-list">
                    {#if animal.fun_facts && animal.fun_facts.length}
                        {#each animal.fun_facts as fact, i}
                            <li class="funfact-item">
                                <span class="funfact-number">{i + 1}</span>
                                <span class="funfact-text">{fact}</span>
                            </li>
                        {/each}
                    {:else}
                        <li>No fun facts available.</li>
                    {/if}
                </ul>
            </div>
            <div class="card-bottom">{animal.habitat}</div>
        </div>
    </div>
</div>

<style>
    :root {
        --card-background-color: #f4f3ed;
        --card-dark-color: #111;
        --thick-line-strength: 1px;
    }

    .border-bottom {
        border-bottom: var(--thick-line-strength) solid var(--card-dark-color);
    }

    .border-bottom-white {
        border-bottom: var(--thick-line-strength) solid
            var(--card-background-color);
    }

    .group-A {
        --group-color: #7d982a;
    }

    .group-B {
        --group-color: #ff3c3c;
    }

    .group-C {
        --group-color: #ceba60;
    }

    .group-D {
        --group-color: #5f9ea0;
    }

    .group-E {
        --group-color: #437ce6;
    }

    .group-F {
        --group-color: #27af86;
    }

    .group-G {
        --group-color: #92643c;
    }

    .group-H {
        --group-color: #2cc7db;
    }

    .card-wrapper {
        perspective: 1000px;
        background-color: var(--website-background-color);
        padding: 0.5rem;
        border-radius: 1rem;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        transition: all 0.3s ease-in-out;
        box-sizing: border-box;
        margin: 0;
        cursor: pointer;
        position: relative;
        width: 320px;
        height: 470px;
    }

    .card {
        transform-style: preserve-3d;
        transition: transform 0.6s ease;
        width: 100%;
        height: 100%;
        position: relative;
    }

    .card.flipped {
        transform: rotateY(180deg);
    }

    .card-front,
    .card-back {
        backface-visibility: hidden;
        position: absolute;
        width: 100%;
        height: 100%;
        border-radius: 1em;
        display: flex;
        justify-content: center;
        align-items: center;
        text-align: center;
        grid-template-columns: repeat(6, 1fr);
        grid-template-rows: repeat(12, 1fr);
        background-color: var(--card-background-color);
        border: 1px solid var(--card-dark-color); /* Ensure no border is visible */
    }

    .card-front {
        display: grid;
        color: var(--card-dark-color);
        overflow: hidden;
    }

    .card-back {
        color: var(--card-dark-color);
        transform: rotateY(180deg);
        display: grid;
        overflow: hidden;
        width: 100%;
        height: 100%;
    }

    .card-back-header {
        grid-column: 1 / -1;
        grid-row: 1;
        background-color: var(--group-color);
        width: 100%;
        height: 130%;
    }

    .card-back-title {
        grid-column: 2 / 6;
        grid-row: 1;
        padding-left: 1.7rem;
        padding-top: 0.8rem;
        display: flex;
        align-items: center;
        text-transform: uppercase;
        font-weight: 600;
        font-size: 1rem;
        margin-top: 0.8rem;
    }

    .card-back-number {
        position: absolute;
        top: -0.5rem;
        left: 1rem;
        transform: translateY(50%);
        display: flex;
        justify-content: center;
        align-items: center;
        color: var(--card-background-color);
        font-weight: 700;
        font-size: 1.2rem;
        border-radius: 0.6em;
        width: 2.5em;
        height: 2.6em;
        background-color: var(--group-color);
        border: 1px solid var(--card-dark-color);
        box-shadow: 0 0 0 3px var(--card-background-color);
    }

    .card-back-content {
        display: grid;
        grid-column: 1 / -1;
        grid-row: span 10;
        align-items: start;
        padding: 1rem;
        height: 90%;
        margin-left: -2.5rem;
        margin-top: -2.5rem;
    }

    .funfact-list {
        text-align: left;
        font-size: 0.68rem;
        line-height: 1.2;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: flex-start;
        gap: 0.2rem;
    }

    .funfact-item {
        display: flex;
        align-items: baseline;
    }

    .funfact-number {
        background-color: var(--group-color);
        color: white;
        font-weight: bold;
        border-radius: 50%;
        width: 1.5rem;
        height: 1.5rem;
        display: flex;
        justify-content: center;
        align-items: center;
        margin-right: 0.5rem;
        font-size: 0.8rem;
    }

    .funfact-text {
        flex: 1;
        line-height: 1.5;
        font-size: 0.65rem;
        padding-left: 0.5rem;
    }

    .card-wrapper:hover {
        transform: translateY(-10px);
        border: solid 1px var(--card-background-color);
        box-shadow: 0 6px 12px rgba(255, 255, 255, 0.1);
    }

    .card-image {
        grid-column: span 6;
        grid-row: span 7;
        width: 100%;
        height: 100%;
        max-width: 100%;
        max-height: 100%;
        object-fit: cover;
    }

    .card-number {
        position: absolute;
        top: 59.3%;
        left: 1rem;
        transform: translateY(-50%);
        display: flex;
        justify-content: center;
        align-items: center;
        color: var(--card-background-color);
        font-weight: 700;
        font-size: 1.2rem;
        border-radius: 0.6em;
        width: 2.1em;
        height: 2.1em;
        background-color: var(--group-color);
        border: 1px solid var(--card-dark-color);
        box-shadow: 0 0 0 3px var(--card-background-color);
    }

    .card-title {
        grid-column: span 5;
        grid-row: 8;
        display: flex;
        align-items: center;
        padding-left: 4rem;
        text-transform: uppercase;
        font-weight: 600;
        font-size: 1rem;
    }

    .card-trivia {
        grid-column: span 6;
        grid-row: 9;
        text-align: left;
        padding-left: 4rem;
        padding-right: 1rem;
        font-size: 0.68rem;
        line-height: 1.2;
        margin-top: -1rem;
        margin-bottom: 0.2rem;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: flex-start;
        height: 85%;
    }

    .groupname {
        grid-column: span 4;
        grid-row: 1;
        display: flex;
        align-items: center;
        justify-content: center;
        position: absolute;
        top: 0.9rem;
        left: 2.5rem;
        padding: 0.35rem 0.5rem;
        z-index: 1;
        color: var(--card-dark-color);
        font-size: 0.75em;
        font-weight: bold;
        text-shadow:
            -0.5px -0.5px 0 #fff,
            0.5px -0.5px 0 #fff,
            -0.5px 0.5px 0 #fff,
            0.5px 0.5px 0 #fff;
    }

    .group-rectangle {
        position: absolute;
        top: 1.7rem;
        left: 1rem;
        transform: translateY(-50%);
        display: flex;
        justify-content: center;
        align-items: center;
        color: var(--card-background-color);
        font-weight: 700;
        font-size: 0.8rem;
        border-radius: 0.6em;
        width: 1.8em;
        height: 1.8em;
        background-color: var(--group-color);
        border: 1px solid var(--card-dark-color);
    }

    .intelligence-box {
        grid-column: 5 / span 2;
        grid-row: 1;
        display: flex;
        align-items: center;
        position: absolute;
        top: 0.9rem;
        right: 1rem;
        background-color: var(--card-dark-color);
        padding: 0.1rem 0.5rem;
        border-radius: 0.4em;
        z-index: 1;
        gap: 0.6rem;
        border: 2px solid var(--group-color);
    }

    .intelligence-icon {
        display: flex;
        align-items: center;
        width: 1em;
        height: 1em;
        color: var(--group-color);
    }

    .intelligence-content {
        font-size: 0.75em;
        font-weight: bold;
        color: var(--card-background-color);
    }

    .stat-box {
        grid-column: span 6;
        grid-row: span 2;
        display: grid;
        grid-template-columns: repeat(6, 1fr);
        grid-template-rows: repeat(2, 1fr);
        gap: 0.5em;
        padding: 0 1rem;
        margin-top: 0.3rem;
    }

    .stat-pair {
        display: grid;
        grid-template-columns: 1fr 1fr;
        background-color: var(--card-background-color);
    }

    .stat-pair:nth-child(1) {
        grid-column: 1 / span 2;
        grid-row: 1;
    }

    .stat-pair:nth-child(2) {
        grid-column: 3 / span 2;
        grid-row: 1;
    }

    .stat-pair:nth-child(3) {
        grid-column: 5 / span 2;
        grid-row: 1;
    }

    .stat-pair:nth-child(4) {
        grid-column: 1 / span 2;
        grid-row: 2;
    }

    .stat-pair:nth-child(5) {
        grid-column: 3 / span 2;
        grid-row: 2;
    }

    .stat-pair:nth-child(6) {
        grid-column: 5 / span 2;
        grid-row: 2;
    }

    .stat-icon {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 2.5em;
        height: 2.5em;
        background-color: #e8e8e3;
        border-radius: 0.6em;
    }

    .stat-content {
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 0.77em;
        font-weight: 500;
        z-index: 10;
    }

    .stat-icon img {
        max-height: 70%;
        max-width: 70%;
        object-fit: cover;
    }

    .card-bottom {
        grid-column: span 6;
        height: 2em;
        font-size: 0.65em;
        display: flex;
        justify-content: center;
        align-items: center;
        text-align: center;
        padding: 0 1em;
        margin-top: 1.2rem;
        background-color: var(--group-color);
        height: 60%;
    }

    .highlight {
        color: var(--group-color);
        font-weight: bold;
    }
</style>