<script lang="ts">
    // Importeer benodigde modules
    import "leaflet/dist/leaflet.css";
    import { onMount } from "svelte";
    import Worldmap from "../components/Worldmap.svelte";
    import Barchart from "../components/Barchart.svelte";
    

    // Definieer het type voor de levensverwachting data
    type LifeExpectancyDataItem = {
        country: string;
        countryCode: string;
        lat: number;
        lng: number;
        lifeExpectancy: { [year: number]: number | null };
    };

    // Verklaringen voor data en states
    let lifeExpectancyData: LifeExpectancyDataItem[] = []; // Data voor levensverwachting
    let filteredLifeExpectancyData: LifeExpectancyDataItem[] = []; // Gefilterde data per jaar
    let selectedYear = 2022; // Standaard geselecteerd jaar
    let labels: string[] = []; // Labels voor de barchart (landen)
    let data: { [year: number]: number[] } = {}; // Data voor de barchart per jaar
    let years: number[] = []; // Lijst van jaren (1960 - 2022)

    // Haal de data op bij het laden van de component
   // In +page.svelte, zorg ervoor dat de data altijd up-to-date is
onMount(async () => {
    try {
        const response = await fetch("/Dataset_datavisualisatie_kopie.csv");
        if (!response.ok) {
            console.error("Fout bij het laden van CSV:", response.status, response.statusText);
            return;
        }
        const csvText = await response.text();
        lifeExpectancyData = convertCSVToGeoData(csvText);
        filterLifeExpectancyByYear(selectedYear);
        labels = lifeExpectancyData.map((item) => item.country);
        years = Object.keys(lifeExpectancyData[0].lifeExpectancy).map((year) => parseInt(year));
        data = {};
        years.forEach((year) => {
            data[year] = lifeExpectancyData.map((item) => Math.round(item.lifeExpectancy[year] ?? 0));
        });
    } catch (error) {
        console.error("Fout bij het ophalen van CSV:", error);
    }


    });

    // Functie om de CSV te converteren naar geo-data
    function convertCSVToGeoData(csvText: string): LifeExpectancyDataItem[] {
        const rows = csvText.split("\n");
        let data: LifeExpectancyDataItem[] = [];

        // Verwerk elke rij uit de CSV
        for (let row of rows) {
            const cells = row.split(";");
            if (cells.length < 5) continue; // Sla rijen zonder voldoende data over

            // Zet de levensverwachting per jaar in een object
            const lifeExpectancy: { [key: number]: number | null } = {};
            for (let year = 1960; year <= 2022; year++) {
                const value = parseFloat(cells[year - 1960 + 4]);
                lifeExpectancy[year] = isNaN(value) ? null : value;
            }

            // Zet de geografische locatie van het land
            const lat = parseFloat(cells[2]);
            const lng = parseFloat(cells[3]);
            if (isNaN(lat) || isNaN(lng)) continue; // Sla rijen zonder geldige coördinaten over

            // Voeg het land toe aan de data
            data.push({
                country: cells[0],
                countryCode: cells[1],
                lat,
                lng,
                lifeExpectancy,
            });
        }

        return data;
    }

    // Functie om data te filteren per jaar
    function filterLifeExpectancyByYear(year: number) {
        selectedYear = year;

        // Filter de data voor de barchart op het geselecteerde jaar
        filteredLifeExpectancyData = lifeExpectancyData.map((item) => ({
            ...item,
            lifeExpectancy: { [year]: item.lifeExpectancy[year] ?? null },
        }));
    }

    // Reageer op veranderingen in geselecteerd jaar
    $: filterLifeExpectancyByYear(selectedYear); // Update bij wijziging van selectedYear
</script>

<main>
    <h1>Levensverwachting</h1>
    <h2>Een overzicht van de levensverwachting per land door de jaren heen.</h2>
    <hr>
    
    <p>Geselecteerd jaar: {selectedYear}</p>

    <!-- Jaar selector -->
    <select
        on:change={(event) =>
            filterLifeExpectancyByYear(
                Number((event.target as HTMLSelectElement).value)
            )
        }
    >
        {#each Array.from({ length: 2022 - 1960 + 1 }, (_, i) => 1960 + i) as year}
            <option value={year} selected={year === selectedYear}>{year}</option>
        {/each}
    </select>

    <!-- Worldmap component voor het weergeven van de data op de kaart -->
    <div class="worldmap-container">
        <Worldmap mapData={filteredLifeExpectancyData} {selectedYear} />
    </div>

    <h3>Barchart</h3>

    <!-- Barchart component voor het weergeven van de data in een grafiek -->
    <div class="barchart-container">
        <Barchart {labels} {data} {years} {selectedYear}/>
    </div>
</main>

<!-- Styling -->
<style>
    /* Algemeen uiterlijk van de pagina */
    main {
        font-family: Verdana, Geneva, Tahoma, sans-serif;
        background-color: #f9f6ee;
        padding: 1em;
    }

    /* Titel en sub-titel stijl */
    h2 {
        padding-bottom: 1em;
        font-size: 1em;
        font-weight: 100;
        margin-top: -1em;
    }

    /* Stijl voor de worldmap-container */
    .worldmap-container {
        padding: 1em;
    }

    /* Stijl voor de barchart-container */
    .barchart-container {
        padding-left: 1em;
        padding-right: 1em;
    }

    /* Styling voor de jaar selector */
    select {
        padding: 0.5em;
        margin: 1em 0;
        font-size: 1rem;
        border-radius: 5px;
        border: 1px solid #ccc;
    }
</style>