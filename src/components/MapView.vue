<template>
  <div class="map-container">
    <div>
      <div id="map" style="height: 500px; width: 100%"></div>
      <div class="controls">
        <label>
          <input type="checkbox" v-model="showPoints" /> Show Points
        </label>
        <label>
          <input type="checkbox" v-model="showPolygons" /> Show Polygons
        </label>
      </div>
    </div>
  </div>
</template>

<script>
import L from "leaflet";
import "leaflet/dist/leaflet.css";

export default {
  name: "MapComponent",
  data() {
    return {
      iconClass: "fa fa-map-marker",
      map: null,
      showPoints: true,
      showPolygons: true,
      pointLayer: null,
      polygonLayer: null,
      selectedFeature: null,
      
      spatialData: {
        points: [
          { lat: 51.505, lng: -0.09, name: "Point 1", population: 500 },
          { lat: 51.515, lng: -0.1, name: "Point 2", population: 1200 },
          { lat: 51.525, lng: -0.08, name: "Point 3", population: 800 },
          { lat: 51.535, lng: -0.07, name: "Point 4", population: 600 },
          { lat: 51.545, lng: -0.06, name: "Point 5", population: 300 },
          { lat: 51.555, lng: -0.05, name: "Point 6", population: 900 },
          { lat: 51.565, lng: -0.04, name: "Point 7", population: 700 },
          { lat: 51.575, lng: -0.03, name: "Point 8", population: 1100 },
          { lat: 51.585, lng: -0.02, name: "Point 9", population: 600 },
          { lat: 51.595, lng: -0.01, name: "Point 10", population: 1400 },
        ],
        polygons: [
          {
            name: "Area 1",
            coordinates: [
              [51.505, -0.09],
              [51.505, -0.12],
              [51.52, -0.12],
              [51.52, -0.09],
            ],
            population: 1000,
            lat: 51.5125,
            lng: -0.105,
          },
          {
            name: "Area 2",
            coordinates: [
              [51.515, -0.13],
              [51.51, -0.14],
              [51.52, -0.15],
              [51.525, -0.13],
              [51.52, -0.12],
            ],
            population: 800,
            lat: 51.517,
            lng: -0.135,
          },
          {
            name: "Area 3",
            coordinates: [
              [51.53, -0.1],
              [51.53, -0.14],
              [51.54, -0.14],
              [51.54, -0.1],
              [51.535, -0.08],
            ],
            population: 1200,
            lat: 51.535,
            lng: -0.115,
          },
          {
            name: "Area 4",
            coordinates: [
              [51.545, -0.09],
              [51.545, -0.12],
              [51.55, -0.13],
              [51.555, -0.1],
              [51.55, -0.08],
            ],
            population: 500,
            lat: 51.55,
            lng: -0.105,
          },
          {
            name: "Area 5",
            coordinates: [
              [51.56, -0.11],
              [51.565, -0.12],
              [51.57, -0.1],
              [51.565, -0.08],
              [51.56, -0.09],
            ],
            population: 800,
            lat: 51.565,
            lng: -0.105,
          },
          {
            name: "Area 6",
            coordinates: [
              [51.575, -0.14],
              [51.58, -0.13],
              [51.585, -0.12],
              [51.58, -0.11],
              [51.575, -0.12],
            ],
            population: 900,
            lat: 51.58,
            lng: -0.125,
          },
          {
            name: "Area 7",
            coordinates: [
              [51.59, -0.15],
              [51.595, -0.14],
              [51.6, -0.13],
              [51.595, -0.12],
              [51.59, -0.13],
            ],
            population: 1100,
            lat: 51.595,
            lng: -0.135,
          },
          {
            name: "Area 8",
            coordinates: [
              [51.605, -0.1],
              [51.61, -0.09],
              [51.615, -0.1],
              [51.61, -0.11],
            ],
            population: 700,
            lat: 51.61,
            lng: -0.105,
          },
          {
            name: "Area 9",
            coordinates: [
              [51.62, -0.15],
              [51.625, -0.14],
              [51.63, -0.12],
              [51.625, -0.1],
              [51.62, -0.11],
            ],
            population: 1300,
            lat: 51.625,
            lng: -0.125,
          },
          {
            name: "Area 10",
            coordinates: [
              [51.635, -0.16],
              [51.64, -0.14],
              [51.645, -0.12],
              [51.64, -0.1],
              [51.635, -0.12],
            ],
            population: 600,
            lat: 51.64,
            lng: -0.13,
          },
        ],
      },
    };
  },
  mounted() {
    this.initializeMap();
    this.createLayers();
  },
  watch: {
    showPoints: "togglePointsLayer",
    showPolygons: "togglePolygonLayer",
  },
  methods: {
    initializeMap() {
      this.map = L.map("map").setView([51.505, -0.09], 13);
      L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution:
          '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
      }).addTo(this.map);
    },

    createLayers() {
      this.pointLayer = L.layerGroup();
      this.polygonLayer = L.layerGroup();

      this.spatialData.points.forEach((point) => {
        const marker = L.marker([point.lat, point.lng], {
          icon: L.divIcon({
            className: "custom-icon", 
            html: `<i class="${this.iconClass}"></i>`, 
            iconSize: [32, 32], 
            iconAnchor: [16, 32], 
            popupAnchor: [0, -32], 
          }),
        });
        marker.bindPopup(
          `Point: ${point.name}<br>Population: ${point.population}`
        );
        marker.on("click", () => this.selectFeature("point", point));
        this.pointLayer.addLayer(marker);
      });

      const colors = ["blue", "green", "red", "purple", "orange"];
      this.spatialData.polygons.forEach((polygon, index) => {
        const poly = L.polygon(polygon.coordinates, {
          color: colors[index % colors.length],
          fillColor: colors[index % colors.length],
          fillOpacity: 0.5,
        });
        poly.bindPopup(
          `Area Name: ${polygon.name}<br>Population: ${polygon.population}`
        );
        poly.on("mouseover", (e) => {
          const layer = e.target;
          layer
            .bindTooltip(
              `
                <b>Area Details</b><br>
                Area Name: ${polygon.name}<br>
                Population: ${polygon.population}<br>
                Latitude: ${polygon.lat}<br>
                Longitude: ${polygon.lng}
              `,
              {
                sticky: true,
              }
            )
            .openTooltip();
        });

        poly.on("mouseout", () => {
          poly.closeTooltip();
        });

        poly.on("click", () => this.selectFeature("polygon", polygon));
        this.polygonLayer.addLayer(poly);
      });

      if (this.showPoints) this.pointLayer.addTo(this.map);
      if (this.showPolygons) this.polygonLayer.addTo(this.map);
    },
    selectFeature(type, feature) {
      this.selectedFeature = { type, ...feature };
    },
    togglePointsLayer() {
      if (this.showPoints) {
        this.pointLayer.addTo(this.map);
      } else {
        this.map.removeLayer(this.pointLayer);
      }
    },
    togglePolygonLayer() {
      if (this.showPolygons) {
        this.polygonLayer.addTo(this.map);
      } else {
        this.map.removeLayer(this.polygonLayer);
      }
    },
  },
};
</script>

<style scoped>
#map {
  height: 500px;
  width: 100%;
}
.data-card {
  position: absolute;
  top: 20px;
  right: 20px;
  background: rgba(255, 255, 255, 0.8);
  padding: 15px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
  width: 250px;
}
.controls {
  position: absolute;
  top: 20px;
  left: 20px;
  background: rgba(255, 255, 255, 0.8);
  padding: 10px;
  border-radius: 8px;
  z-index: 1000;
  display: flex;
  flex-direction: row;
}
.controls label {
  display: block;
}
#map {
  height: 500px;
  width: 100%;
}
.custom-icon i {
  font-size: 80px; 
  color: red;
}
</style>
