<template>
  <div>
    <div id="mapContainer" class="map"></div>
    <div :class="{ 'drawer--is-active': drawerOpen }" class="drawer fixed">
      <h2 class="text-xl text-gray-900 leading-tight">title</h2>
      <p class="text-base text-gray-600 leading-normal">content</p>
      <button
        class="w-auto block bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
      >
        Watch Video
      </button>
      <span @click="drawerOpen = false" class="close">
        &times;
      </span>
    </div>
  </div>
</template>

<script>
import mapboxgl from 'mapbox-gl'

export default {
  components: {},

  data() {
    return {
      accessToken:
        'pk.eyJ1Ijoic2JpbGxpbmdoYW1tYXAiLCJhIjoiY2ticWtoMGdtMDhldzMxbnQzbmt5ZHY0dSJ9.4sFiMDEQ5QA_3yRoqCDqiw',

      drawerOpen: false,
    }
  },

  mounted() {
    const app = this
    mapboxgl.accessToken = this.accessToken

    const map = new mapboxgl.Map({
      container: 'mapContainer',
      style: 'mapbox://styles/sbillinghammap/ckbqjqxcv51i31in0vqbxk82p',
      center: [14.5682, 68.2343],
      zoom: 6.2,
      maxBounds: [
        [12.2, 66.6],
        [15.8, 69.5],
      ],
    })

    const nav = new mapboxgl.NavigationControl()
    map.addControl(nav, 'top-right')

    map.on('style.load', function() {
      // Add vector to map
      map.loadImage('pin.png', (error, image) => {
        if (error) throw error
        map.addImage('pin', image)
        map.addSource('point', {
          type: 'geojson',
          data: {
            type: 'FeatureCollection',
            features: [
              {
                type: 'Feature',
                geometry: {
                  type: 'Point',
                  coordinates: [14.5682, 68.2343],
                },
                properties: {
                  title: 'Mapbox',
                  description: 'Washington, D.C.',
                },
              },
              {
                type: 'Feature',
                geometry: {
                  type: 'Point',
                  coordinates: [14.4682, 68.3343],
                },
                properties: {
                  title: 'Mapbox',
                  description: 'Washington, D.C.',
                },
              },
              {
                type: 'Feature',
                geometry: {
                  type: 'Point',
                  coordinates: [14.0682, 68.3343],
                },
              },
              {
                type: 'Feature',
                geometry: {
                  type: 'Point',
                  coordinates: [13.3682, 68.0343],
                },
              },
              {
                type: 'Feature',
                geometry: {
                  type: 'Point',
                  coordinates: [13.3482, 68.0343],
                },
              },
              {
                type: 'Feature',
                geometry: {
                  type: 'Point',
                  coordinates: [13.3382, 68.0343],
                },
                properties: {
                  title: 'Mapbox',
                  description: 'Washington, D.C.',
                },
              },
            ],
          },
          cluster: true,
          clusterMaxZoom: 14, // Max zoom to cluster points on
          clusterRadius: 10, // Radius of each cluster when clustering points (defaults to 50)
        })

        map.addLayer({
          id: 'points',
          type: 'symbol',
          source: 'point',
          layout: {
            'icon-allow-overlap': true,
            'icon-image': 'pin',
            'icon-size': [
              'interpolate',
              ['linear'],
              ['zoom'],
              0.2,
              0.7,
              2.25,
              0.4,
            ],
          },
        }) // 'natural-point-label',

        map.on('dragstart', function() {
          app.drawerOpen = false
        })

        map.on('click', function(e) {
          const features = map.queryRenderedFeatures(e.point, {
            layers: ['points'], // replace this with the name of the layer
          })

          if (!features.length) {
            return
          }

          const feature = features[0]

          app.drawerOpen = true
          console.log(feature.properties.title, feature.properties.description)

          map.flyTo({
            center: feature.geometry.coordinates,
            zoom: 11,
            speed: 1.5,
            offset: [0, -150],
          })

          // new mapboxgl.Popup({ offset: [0, -15] })
          //   .setLngLat(feature.geometry.coordinates)
          //   .setHTML(
          //     '<h3>' +
          //       feature.properties.title +
          //       '</h3><p>' +
          //       feature.properties.description +
          //       '</p>',
          //   )
          //   .addTo(map)
        })
      })
    })
  },
}
</script>

<style>
.map {
  position: fixed;
  width: 100%;
  height: 100%;
}

/* Sample `apply` at-rules with Tailwind CSS
.container {
  @apply min-h-screen flex justify-center items-center text-center mx-auto;
}
*/
.drawer {
  bottom: 0;
  left: 0;
  width: 100%;
  height: 45vh;
  background: #fff;
  padding: 2rem;
  transform: translateY(100vh);
  transition: 300ms ease-in-out;
  position: relative;
}
.drawer--is-active {
  transform: translateY(55vh);
}

.close {
  position: absolute;
  padding: 1rem;
  font-size: 1.8rem;
  top: 0;
  right: 0;
}
</style>
