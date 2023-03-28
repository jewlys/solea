<template>
  <div>
    <div ref="map" class="map-container"></div>
  </div>
</template>

<script>
import mapboxgl from 'mapbox-gl';

export default {
  name: 'Map',
  data() {
    return {
      map: null,
      bars: [
        {
          name: "Solea",
          coordinates: [2.1744, 41.3851],
          isSunny: true,
          hasTerrace: true,
        },
        {
          name: "Bar 2",
          coordinates: [2.1669, 41.3851],
          isSunny: false,
          hasTerrace: false,
        },
        // Add more bars here
      ],
    }
  },
  mounted() {
    mapboxgl.accessToken = 'pk.eyJ1IjoiamV3bHlzIiwiYSI6IkdTYzBkMEEifQ.8s_QzufEYMC1fKC0U8PtbQ';
    this.map = new mapboxgl.Map({
      container: this.$refs.map,
      style: 'mapbox://styles/mapbox/streets-v11',
      center: [2.1685, 41.3891], // Barcelona coordinates
      zoom: 13,
    });
    
    // Add 3D shading
    const mapboxTerrain = 'mapbox://mapbox.mapbox-terrain-v2';
    this.map.on('load', () => {
      this.map.addSource('mapbox-dem', {
        'type': 'raster-dem',
        'url': mapboxTerrain,
        'tileSize': 512,
        'maxzoom': 14
      });
      this.map.setTerrain({ 'source': 'mapbox-dem', 'exaggeration': 1.5 });
      this.map.addLayer({
        'id': 'hillshading',
        'source': 'mapbox-dem',
        'type': 'hillshade'
      }, 'waterway-label');
    });
    
    // Add bars to the map
    this.bars.forEach((bar) => {
      const markerElement = document.createElement('div');
      markerElement.className = `marker ${bar.isSunny ? 'sunny' : 'shady'} ${bar.hasTerrace ? 'terrace' : ''}`;
      
      new mapboxgl.Marker({
        element: markerElement,
        anchor: 'bottom',
      }).setLngLat(bar.coordinates).addTo(this.map);
    });
  },
};
</script>

<style>
.map-container {
  height: 100vh;
}

.marker {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  border: 2px solid white;
}

.marker.sunny {
  background-color: yellow;
}

.marker.shady {
  background-color: gray;
}

.marker.terrace:before {
  content: '🌞';
  font-size: 12px;
}
</style>