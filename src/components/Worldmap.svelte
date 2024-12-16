<script lang="ts">
    import "leaflet/dist/leaflet.css"; // Importeren van de CSS
    import { onMount } from "svelte"; // Haal onMount uit Svelte

    export let mapData: {
        country: string;
        countryCode: string;
        lat: number;
        lng: number;
        lifeExpectancy: { [year: number]: number | null };
    }[] = [];
    export let selectedYear: number;

    let mapContainer: HTMLDivElement;

    // Declareren van 'leaf' als 'any' om typefouten te vermijden
    let leaf: any;
    let map: any;
    let markersLayer: any;

    // Dynamische import van Leaflet
    onMount(async () => {
        // Dynamisch importeren van Leaflet
        leaf = (await import("leaflet")).default;

        // Maak de kaart aan
        map = leaf.map(mapContainer, {
            center: [20, 0],
            zoom: 2,
            minZoom: 2,
            maxZoom: 4,
            dragging: false,
        });

        // Voeg de OpenStreetMap-laag toe
        leaf.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
            attribution:
                '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
        }).addTo(map);

        // Maak een laag voor de markers
        markersLayer = leaf.layerGroup().addTo(map);

        // Voeg een 'ready' event listener toe om de kaartgrootte te herberekenen
        map.whenReady(() => map.invalidateSize());
    });

    // Maak de markers opnieuw aan telkens wanneer de data verandert
    $: {
        if (map && markersLayer) {
            updateMarkers();
        }
    }

    // Functie die markers updatet
    function updateMarkers() {
        if (!map) return;

        markersLayer.clearLayers(); // Verwijder bestaande markers

        mapData.forEach((item) => {
            const lifeExpectancyYear = item.lifeExpectancy[selectedYear];

            if (lifeExpectancyYear === null || isNaN(lifeExpectancyYear)) return;

            const color = getColorForLifeExpectancy(lifeExpectancyYear);

            leaf.circleMarker([item.lat, item.lng], {
                color: color,
                radius: 8,
                weight: 2,
                fillOpacity: 0.6,
            })
                .bindPopup(
                    `<b>${item.country}</b><br>Levensverwachting in ${selectedYear}: ${Math.round(lifeExpectancyYear)}`
                )
                .addTo(markersLayer);
        });
    }

    // Functie om de kleur te bepalen op basis van levensverwachting
    function getColorForLifeExpectancy(lifeExpectancy: number) {
        if (lifeExpectancy > 80) return "#008000"; // Groen voor hoge levensverwachting
        if (lifeExpectancy > 70) return "#FFA500"; // Oranje voor middelhoge levensverwachting
        return "#D2042D"; // Rood voor lage levensverwachting
    }
</script>

<!-- Container voor de kaart -->
<div bind:this={mapContainer} style="width: 100%; height: 80vh;"></div>