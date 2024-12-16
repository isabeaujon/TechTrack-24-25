<script lang="ts">
    // Exporteer de benodigde props
    export let labels: string[] = []; // Lijst van labels voor de barchart (landen)
    export let data: { [year: number]: number[] } = {}; // Data voor de barchart per jaar
    export let years: number[] = []; // Lijst van beschikbare jaren
    export let selectedYear: number;  // Verander dit naar een prop


    
    // Houdt het geselecteerde jaar bij (standaard is het eerste jaar in de lijst)
    let width = Math.max(700, labels.length * 40); // Dynamische breedte op basis van het aantal labels
    let height = 400; // Standaard hoogte van de grafiek
    const barWidth = 30; // Breedte van elke staaf
    const spacing = 10; // Ruimte tussen de staven

    // Reactive statement om de data voor het geselecteerde jaar bij te werken
    $: chartData = data[selectedYear] || [];

    // Functie om het geselecteerde jaar te updaten wanneer de slider wordt aangepast
    function updateYear(event: Event) {
        const slider = event.target as HTMLInputElement;
        selectedYear = parseInt(slider.value);
    }

    // Schaalfuncties voor de X- en Y-as
    let xScale = (index: number) => index * (barWidth + spacing);
    let yScale = (value: number) => height - value * 3; // Schaal de Y-waarden voor visuele weergave

    function getColorForLifeExpectancy(lifeExpectancy: number) {
    if (lifeExpectancy > 80) return "#008000"; // Groen voor hoge levensverwachting
    if (lifeExpectancy > 70) return "#FFA500"; // Oranje voor middelhoge levensverwachting
    return "#D2042D"; // Rood voor lage levensverwachting
}
</script>



<div>
    <!-- Scroll-container voor de grafiek, zorgt ervoor dat de grafiek horizontaal gescrold kan worden -->
    <div class="scroll-container">
        <!-- SVG voor de barchart, de grafiek wordt getekend in deze SVG -->
        <svg {width} {height}>
            <!-- Y-as labels en lijnen -->
            <g>
                {#each [0, 20, 40, 60, 80, 100] as yValue}
                    <text x="0" y={yScale(yValue) + 5} class="y-axis-label">
                        {yValue}
                        <!-- Weergave van het Y-as label -->
                    </text>
                    <!-- Lijnen die de verschillende Y-as niveaus representeren -->
                    <line
                        x1="30"
                        x2={width}
                        y1={yScale(yValue)}
                        y2={yScale(yValue)}
                        stroke="lightgray"
                    />
                {/each}
            </g>

            <!-- Teken de staven voor de barchart op basis van de geselecteerde data -->
            {#each chartData as value, index}
                <!-- Commentaar buiten de tag -->
                <rect
                    x={xScale(index) + 40}
                    y={yScale(value)}
                    width={barWidth}
                    height={value * 3}
                    fill={getColorForLifeExpectancy(value)} 
                />
                <!-- X-positie van de staaf -->
                <!-- Y-positie van de staaf -->
                <!-- Breedte van de staaf -->
                <!-- Hoogte van de staaf (schaal) -->
                <!-- Kleur van de staaf -->

                <!-- Voeg de waarde boven de staaf toe -->
                <text
                    x={xScale(index) + 55}
                    y={yScale(value) - 5}
                    text-anchor="middle"
                    class="bar-label"
                >
                    <!-- Weergave van de waarde boven de staaf -->
                </text>
            {/each}
        </svg>

        <!-- Labels onder de staven, worden verticaal weergegeven onder de staven -->
        <div class="label-container">
            {#each labels as label, index}
                <span
                    class="label"
                    style="left: {xScale(index) +
                        55}px; transform: rotate(-90deg); transform-origin: left bottom;"
                >
                    {label}
                    <!-- Weergave van het label onder de staaf -->
                </span>
            {/each}
        </div>
    </div>

    <!-- Slider voor het selecteren van het jaar -->
    <div>
        <label for="yearSlider"
            >Slide tussen de jaren een jaar: {selectedYear}</label
        >
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

    <div class="legend">
        <div><span style="background-color: #008000;"></span> &gt; 80</div>
        <div><span style="background-color: #FFA500;"></span> 70 - 80</div>
        <div><span style="background-color: #D2042D;"></span> &lt; 70</div>
    </div>
    

</div>



<!-- Styling voor de barchart -->
<style>
    /* Scroll-container voor de grafiek, maakt horizontale scroll mogelijk */
    .scroll-container {
        width: 100%;
        overflow-x: auto; /* Zorg ervoor dat de grafiek horizontaal gescrold kan worden */
        padding: 10px 0;
        position: relative;
    }

    /* Container voor de labels onder de grafiek */
    .label-container {
        position: absolute;
        bottom: 30px;
        left: 40px;
        width: 100%;
        color: #f9f6ee;
        white-space: nowrap;
    }

    /* Stijl voor elk label onder de staaf */
    .label {
        position: absolute;
        font-size: 14px;
        text-align: left;
        font-weight: 500;
    }

    /* Stijl voor de Y-as labels */
    .y-axis-label {
        font-size: 12px;
        text-anchor: middle;
        fill: gray;
        transform: translateX(10px); /* Verplaats de tekst iets naar rechts */
    }

    /* Algemeen styling voor de SVG-grafiek */
    svg {
        margin: 0 auto;
        overflow: visible;
    }

    .legend {
    display: flex;
    margin-top: 10px;
    }

    .legend div {
    margin-right: 20px;
    }

    .legend span {
    width: 15px;
    height: 15px;
    display: inline-block;
    margin-right: 5px;
    }

</style>