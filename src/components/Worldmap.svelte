<script lang="ts">
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

    onMount(async () => {
        const leaf = (await import("leaflet")).default;

        // Maak een kaart en zet het centrum en de zoom in
        const map = leaf
            .map(mapContainer, {
                center: [20, 0], // Zet de kaart op het middelpunt van de wereld
                zoom: 2, // Beginzoomniveau
                minZoom: 2,
                maxZoom: 4,
                dragging: false,
            })
            .setView([20, 0], 2);

        // Voeg OpenStreetMap tegel-laag toe
        leaf.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
            attribution:
                '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
        }).addTo(map);

        // Maak een nieuwe laag aan voor de markers
        let markersLayer = leaf.layerGroup().addTo(map);

        updateMarkers();

        // Functie om markers te updaten
        function updateMarkers() {
            markersLayer.clearLayers(); // Verwijder bestaande markers

            mapData.forEach((item) => {
                const lifeExpectancyYear = item.lifeExpectancy[selectedYear];

                if (lifeExpectancyYear === null || isNaN(lifeExpectancyYear))
                    return;

                const color = getColorForLifeExpectancy(lifeExpectancyYear);

                // Maak een nieuwe marker aan
                const marker = leaf.circleMarker([item.lat, item.lng], {
                    color: color, // Kleur van de marker
                    radius: 8, // Grootte van de marker
                    weight: 2,
                    fillOpacity: 0.6,
                });

                marker
                    .bindPopup(
                        `<b>${item.country}</b><br>Levensverwachting in ${selectedYear}: ${Math.round(
                            lifeExpectancyYear,
                        )}`,
                    )
                    .addTo(markersLayer); // Voeg marker toe aan de laag
            });
        }

        // Update markers bij mount en wanneer de kaart geladen is
        updateMarkers();
        map.whenReady(() => map.invalidateSize());
    });

    // Functie die kleur bepaalt op basis van levensverwachting
    function getColorForLifeExpectancy(lifeExpectancy: number) {
        if (lifeExpectancy > 80) return "#008000"; // Groen voor hoge levensverwachting
        if (lifeExpectancy > 70) return "#FFA500"; // Oranje voor middelhoge levensverwachting
        return "#D2042D"; // Rood voor lage levensverwachting
    }
</script>

<!-- Container voor de kaart -->
<div bind:this={mapContainer} style="width: 100%; height: 80vh;"></div>
