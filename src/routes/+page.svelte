<script lang="ts">
    // Import
    import "leaflet/dist/leaflet.css";
    import { onMount } from "svelte";
    import Worldmap from "../components/Worldmap.svelte";
    import Barchart from "../components/Barchart.svelte";

    type LifeExpectancyDataItem = {
        country: string;
        countryCode: string;
        lat: number;
        lng: number;
        lifeExpectancy: { [year: number]: number | null };
    };

    let lifeExpectancyData: LifeExpectancyDataItem[] = [];
    let filteredLifeExpectancyData: LifeExpectancyDataItem[] = []; // Voor filtered data
    let selectedYear = 2022;
    let labels: string[] = []; // Voor de bar chart labels (landen)
    let data: { [year: number]: number[] } = {}; // Data voor de bar chart
    let years: number[] = []; // De beschikbare jaren (bijv. 1960-2022)

    onMount(async () => {
        try {
            const response = await fetch(
                "./Dataset_datavisualisatie_kopie.csv",
            );
            if (!response.ok) {
                console.error(
                    "Failed to load CSV:",
                    response.status,
                    response.statusText,
                );
                return;
            }

            const csvText = await response.text();
            console.log("CSV Loaded:", csvText);

            lifeExpectancyData = convertCSVToGeoData(csvText);
            filterLifeExpectancyByYear(selectedYear); // Eerste keer filteren

            // Zet de labels en data voor de barchart
            labels = lifeExpectancyData.map((item) => item.country);
            years = Object.keys(lifeExpectancyData[0].lifeExpectancy).map(
                (year) => parseInt(year),
            );
            data = {};

            years.forEach((year) => {
                data[year] = lifeExpectancyData.map(
                    (item) => item.lifeExpectancy[year] ?? 0,
                );
            });
        } catch (error) {
            console.error("Error fetching CSV:", error);
        }
    });

    function convertCSVToGeoData(csvText: string): LifeExpectancyDataItem[] {
        const rows = csvText.split("\n");
        let data: LifeExpectancyDataItem[] = [];

        for (let row of rows) {
            const cells = row.split(";");
            if (cells.length < 5) continue;

            const lifeExpectancy: { [key: number]: number | null } = {};
            for (let year = 1960; year <= 2022; year++) {
                const value = parseFloat(cells[year - 1960 + 4]);
                lifeExpectancy[year] = isNaN(value) ? null : value;
            }

            const lat = parseFloat(cells[2]);
            const lng = parseFloat(cells[3]);
            if (isNaN(lat) || isNaN(lng)) continue;

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

    function filterLifeExpectancyByYear(year: number) {
        selectedYear = year;
        filteredLifeExpectancyData = lifeExpectancyData.map((item) => ({
            ...item,
            lifeExpectancy: { [year]: item.lifeExpectancy[year] ?? null },
        }));
    }

    // Reactieve declaratie
    $: filterLifeExpectancyByYear(selectedYear); // Update bij elke wijziging van selectedYear
</script>

<main>
    <h1>Levensverwachting</h1>
    <h2>Een overzicht van de levensverwachting per land door de jaren heen.</h2>
    
    
    <p>Geselecteerd jaar: {selectedYear}</p>

    <!-- Jaar selector -->
    <select
        on:change={(event) =>
            filterLifeExpectancyByYear(
                Number((event.target as HTMLSelectElement).value),
            )}
    >
        {#each Array.from({ length: 2022 - 1960 + 1 }, (_, i) => 1960 + i) as year}
            <option value={year} selected={year === selectedYear}>{year}</option
            >
        {/each}
    </select>

    <!-- Worldmap component die de data ontvangt -->
    <div class="worldmap-container">
        <Worldmap mapData={lifeExpectancyData} {selectedYear} />
    </div>

    <h3>Barchart</h3>

    <!-- Barchart component die de data ontvangt -->
    <div class="barchart-container">
        <Barchart {labels} {data} {years} />
    </div>

</main>





<!--Styling-->
<style>
    main {
        font-family: Verdana, Geneva, Tahoma, sans-serif;
        background-color: #f9f6ee;
        padding: 1em;
    }

    h2 {
        padding-bottom: 1em;
        font-size: 1em;
        font-weight: 100;
        margin-top: -0.5;
    }

    .worldmap-container {
        padding: 1em;
    }

    .barchart-container {
        padding-left: 1em;
        padding-right: 1em;
    }
</style>
