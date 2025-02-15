<template>
  <div id="map"></div>
</template>

<script>
import { shallowRef, onMounted, onBeforeUnmount, nextTick } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";
import { emitter } from "./MapTopbar.vue";
import { locations } from "../locations.js";

export default {
  name: "MapView",
  setup() {
    const map = shallowRef(null);
    const markerLayer = shallowRef(null);

    const initMap = () => {
      if (!map.value) {
        map.value = L.map("map", {
          doubleClickZoom: false,
          zoomAnimation: true,
          fadeAnimation: true,
          markerZoomAnimation: true,
        }).setView([34.0522, -118.2437], 12);

        L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
          attribution: "&copy; OpenStreetMap contributors",
        }).addTo(map.value);

        markerLayer.value = L.layerGroup().addTo(map.value);
        addMarkers();
      }
    };

    const addMarkers = () => {
      if (!map.value || !markerLayer.value) return;

      const customIcon = L.icon({
        iconUrl: "https://leafletjs.com/examples/custom-icons/leaf-red.png",
        iconSize: [40, 40],
        iconAnchor: [20, 40],
        popupAnchor: [0, -35],
      });

      Object.values(locations).forEach(category => {
        Object.entries(category).forEach(([name, coords]) => {
          const marker = L.marker([coords.lat, coords.lng], { icon: customIcon })
            .bindPopup(`<div class="popup-content"><h3>${name}</h3></div>`);
          markerLayer.value.addLayer(marker);
        });
      });
    };

    const handleSearch = (location) => {
      const foundLocation = Object.values(locations).flatMap(category => Object.entries(category)).find(([name]) => name === location);
      if (!foundLocation || !map.value) return;
      const [, { lat, lng }] = foundLocation;
      map.value.flyTo([lat, lng], 14, { animate: true, duration: 1.2 });
    };

    onMounted(() => {
      nextTick(() => {
        initMap();
        emitter.on("search-location", handleSearch);
      });
    });

    onBeforeUnmount(() => {
      emitter.off("search-location", handleSearch);
      if (map.value) {
        map.value.remove();
        map.value = null;
      }
    });

    return { map, markerLayer };
  },
};
</script>

<style>
#map {
  height: calc(100vh - 90px);
  z-index: 0;
}

.popup-content {
  text-align: center;
  max-width: 200px;
}

.popup-content h3 {
  margin: 5px 0;
  font-size: 16px;
  color: #333;
}
</style>
