<script lang="ts">
    // De 'labels' zijn de namen van de landen of categorieën die we onder de staven willen tonen
    export let labels: string[] = [];

    // De 'data' is de levensverwachting per jaar voor elk land of categorie
    export let data: { [year: number]: number[] } = {};

    // 'years' bevat de beschikbare jaren
    export let years: number[] = [];

    // De 'selectedYear' houdt het momenteel geselecteerde jaar bij
    let selectedYear = years[0];

    // De afmetingen van de grafiek: breedte en hoogte van de SVG-container
    let width = 700; // Dit kan later dynamisch worden aangepast
    let height = 400; // Dit kan later dynamisch worden aangepast

    // De breedte van elke staaf in de grafiek
    const barWidth = 30; // Dit kan worden aangepast afhankelijk van het aantal staven

    // De ruimte tussen de staven
    const spacing = 10;

    // Reactieve declaratie voor chartData
    $: chartData = data[selectedYear] || [];

    // Functie die de waarde van de slider bijwerkt
    function updateYear(event: Event) {
        const slider = event.target as HTMLInputElement;
        selectedYear = parseInt(slider.value);
    }

    // Dynamisch berekenen van de schaal voor de X- en Y-as
    let xScale = (index: number) => index * (barWidth + spacing);
    let yScale = (value: number) => height - value;
</script>

<div>
    <div class="scroll-container">
        <!-- SVG-container voor de grafiek -->
        <svg width="100%" {height}>
            <!-- Teken de staven -->
            {#each chartData as value, index}
                <rect
                    x={xScale(index)}
                    y={yScale(value)}
                    width={barWidth}
                    height={value}
                    fill="teal"
                />
            {/each}
        </svg>

        <!-- Teken de labels onder de staven -->
        <div class="label-container">
            {#each labels as label, index}
                <span class="label" style="left: {xScale(index)}px;">
                    {label}
                </span>
            {/each}
        </div>
    </div>

    <!-- Slider voor het selecteren van een jaar -->
    <div>
        <label for="yearSlider">Jaar: {selectedYear}</label>
        <input
            id="yearSlider"
            type="range"
            min={Math.min(...years)}
            max={Math.max(...years)}
            step="1"
            bind:value={selectedYear}
            on:input={updateYear}
        />
    </div>
</div>

<main>
    <p>
        Gemaakt door: Isa Beaujon - 500798069 - Minor Information Design - Block
        Tech
    </p>
</main>

<style>
    .scroll-container {
        width: 100%;
        overflow-x: auto; /* Voeg een horizontale scrollbar toe */
        padding: 10px 0;
        position: relative; /* Voor het positioneren van de labels */
    }

    .label-container {
        position: absolute;
        bottom: 0; /* Zorg ervoor dat de labels onder de grafiek komen */
        left: 0;
        width: 100%;
        white-space: nowrap; /* Zorg ervoor dat de labels niet worden afgebroken */
    }

    .label {
        position: absolute;
        font-size: 12px; /* Of een andere grootte */
        text-align: center;
        transform: translateX(-50%);
    }

    svg {
        display: block;
        margin: 0 auto;
        width: 100%;
        max-width: 700px; /* Maximale breedte van de grafiek */
    }

    p {
        font-size: small;
        padding-top: 2em;
    }
</style>
