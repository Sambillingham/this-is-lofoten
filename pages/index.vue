<template>
  <div>
    <Navbar />
    <div id="mapContainer" class="map"></div>
    <div :class="{ 'drawer--is-active': drawerOpen }" class="drawer fixed">
      <h2 class="mb-4 text-xl text-gray-900 leading-tight capitalize">
        {{ drawerTitle }}
      </h2>
      <p class="mb-8 text-base text-gray-600 leading-normal">
        {{ drawerDescription }}
      </p>
      <img v-if="isMobile" :src="drawerPlaceholder" class="mb-4" />
      <div v-else class="iframe-container">
        <iframe
          :src="`https://www.youtube.com/embed/${drawerVideoID}`"
          width="100%"
          height="400px"
          class="mb-4"
          frameborder="0"
          allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
          allowfullscreen
        ></iframe>
      </div>
      <a
        v-if="isMobile"
        :href="`https://www.youtube.com/watch?v=${drawerVideoID}`"
        class="w-auto block bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded text-center"
      >
        Watch Now!
      </a>
      <span @click="drawerOpen = false" class="close cursor-pointer">
        &times;
      </span>
    </div>
  </div>
</template>

<script>
import mapboxgl from 'mapbox-gl'

import { videoData } from '@/assets/js/data'

import Navbar from '@/components/Navbar'

export default {
  components: {
    Navbar,
  },

  data() {
    return {
      accessToken:
        'pk.eyJ1Ijoic2JpbGxpbmdoYW1tYXAiLCJhIjoiY2ticWtoMGdtMDhldzMxbnQzbmt5ZHY0dSJ9.4sFiMDEQ5QA_3yRoqCDqiw',

      drawerOpen: false,
      drawerTitle: '',
      drawerDescription: '',
      drawerPlaceholder: 'http://placehold.it/1280x720',
      drawerVideoID: '',
      windowWidth: window.innerWidth,
    }
  },

  computed: {
    isMobile() {
      return this.windowWidth <= 900
    },
  },

  mounted() {
    window.addEventListener('resize', () => {
      return (this.windowWidth = window.innerWidth)
    })

    const app = this
    mapboxgl.accessToken = this.accessToken

    const map = new mapboxgl.Map({
      container: 'mapContainer',
      style: 'mapbox://styles/sbillinghammap/ckbqjqxcv51i31in0vqbxk82p',
      center: [14.5682, 68.2343],
      zoom: 6.2,
      maxBounds: [
        [11.8, 66.6],
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
            features: videoData.map((v) => {
              return {
                type: 'Feature',
                geometry: {
                  type: 'Point',
                  coordinates: v.coordinates,
                },
                properties: {
                  title: v.title,
                  description: '',
                  videoID: v.videoID,
                },
              }
            }),
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

          app.drawerTitle = feature.properties.title
          app.drawerDescription = feature.properties.description
          app.drawerVideoID = feature.properties.videoID
          app.drawerOpen = true

          const offset = app.isMobile ? [0, -150] : [-150, 0]

          map.flyTo({
            center: feature.geometry.coordinates,
            zoom: 11,
            speed: 1.5,
            offset: [...offset],
          })
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
  display: flex;
  justify-content: center;
  flex-direction: column;
}

.drawer--is-active {
  transform: translateY(55vh);
}

@media screen and (min-width: 900px) {
  .drawer {
    bottom: 0;
    right: 0;
    top: 0;
    transform: translateX(100vw);
    width: 45vw;
    height: 100vh;
  }

  .drawer--is-active {
    transform: translateX(55vw);
  }
}

/* @media (orientation: landscape) {
  .drawer {
    bottom: 0;
    right: 0;
    top: 0;
    transform: translateX(100vw);
    width: 45vw;
    height: 100vh;
  }

  .drawer--is-active {
    transform: translateX(55vw);
  }
} */

.close {
  position: absolute;
  padding: 1rem;
  font-size: 1.8rem;
  top: 0;
  right: 0;
}
.iframe-container {
  overflow: hidden;
  padding-top: 56.25%;
  position: relative;
}

.iframe-container iframe {
  border: 0;
  height: 100%;
  left: 0;
  position: absolute;
  top: 0;
  width: 100%;
}
</style>
