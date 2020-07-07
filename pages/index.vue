<template>
  <div>
    <div
      v-if="initialLoad"
      class="intro"
      :class="{ 'intro--hidden': !initialLoad }"
    >
      <video width="100%" height="100%" loop autoplay muted>
        <source :src="require(`~/assets/video/bg.mp4`)" type="video/mp4" />
      </video>
      <div class="intro__container">
        <header><h1>This Is Lofoten</h1></header>
        <div class="intro__description">
          <p>
            Lorem ipsum dolor sit amet consectetur adipisicing elit. Corporis
            omnis illo sapiente ea in rem doloribus pariatur quibusdam, odit
            suscipit consequuntur quo ducimus consequatur amet! Deleniti amet
            possimus accusamus minus!
          </p>
          <p>
            Lorem ipsum dolor sit amet consectetur adipisicing elit. Corporis
            omnis illo sapiente ea in rem doloribus pariatur quibusdam, odit
            suscipit consequuntur quo ducimus consequatur amet! Deleniti amet
            possimus accusamus minus!
          </p>
        </div>
        <span class="btn" @click="initialLoad = false">
          Continue
        </span>
      </div>
    </div>

    <Navbar />
    <div id="mapContainer" class="map"></div>
    <div :class="{ 'drawer--is-active': drawerOpen }" class="drawer fixed">
      <h2 class="mb-4 text-xl text-gray-900 leading-tight capitalize">
        {{ drawerTitle }}
      </h2>
      <div
        v-if="!isMobile"
        v-html="drawerDescription"
        class="mb-8 text-base text-gray-900 leading-normal"
      ></div>
      <a
        v-if="isMobile"
        :href="`https://www.youtube.com/watch?v=${drawerVideoID}`"
      >
        <img
          v-if="drawerThumbnail"
          :src="require(`~/assets/img/${drawerThumbnail}`)"
          class="mb-4"
        />
      </a>
      <div v-if="!isMobile" class="iframe-container">
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
        >Watch Now!</a
      >
      <span @click="drawerOpen = false" class="close cursor-pointer"
        >&times;</span
      >
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
      drawerThumbnail: '',
      drawerVideoID: '',
      windowWidth: window.innerWidth,
      initialLoad: true,
    }
  },

  computed: {
    isMobile() {
      return this.windowWidth <= 900
    },
  },

  methods: {
    checkCookie() {
      if (
        !document.cookie
          .split('; ')
          .find((row) => row.startsWith('initialLoad'))
      ) {
        this.initialLoad = true
        document.cookie =
          'initialLoad=true; expires=Fri, 31 Dec 9999 23:59:59 GMT'
      }
    },
  },

  mounted() {
    window.addEventListener('resize', () => {
      return (this.windowWidth = window.innerWidth)
    })

    // this.checkCookie()

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
                  description: v.description,
                  videoID: v.videoID,
                  thumbnail: v.thumbnail,
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

        map.addLayer({
          id: 'clusters',
          type: 'circle',
          source: 'point',
          filter: ['has', 'point_count'],
          paint: {
            'circle-color': '#000000',
            'circle-radius': 12,
            'circle-translate': [0, 33],
            'circle-translate-anchor': ''
          },
        })

        map.addLayer({
          id: 'cluster-count',
          type: 'symbol',
          source: 'point',
          filter: ['has', 'point_count'],
          layout: {
            'text-field': '{point_count_abbreviated}',
            'text-font': ['DIN Offc Pro Medium', 'Arial Unicode MS Bold'],
            'text-size': 17,
            'text-offset': [0, 2],
          },
          paint: {
            'text-color': 'white',
          },
        })

        map.on('dragstart', function() {
          app.drawerOpen = false
        })

        map.on('click', 'clusters', function(e) {
          const features = map.queryRenderedFeatures(e.point, {
            layers: ['clusters'],
          })
          const clusterId = features[0].properties.cluster_id

          map
            .getSource('point')
            .getClusterExpansionZoom(clusterId, function(err, zoom) {
              if (err) return
              map.easeTo({
                center: features[0].geometry.coordinates,
                zoom: zoom + 0.5,
                speed: 1.5,
              })
            })
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
          app.drawerThumbnail = feature.properties.thumbnail
          app.drawerVideoID = feature.properties.videoID
          app.drawerOpen = true

          console.log(app.drawerTitle)

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
  display: flex;
  justify-content: center;
  flex-direction: column;
}

.drawer a {
  font-weight: 700;
}

.drawer--is-active {
  transform: translateY(0);
  position: fixed;
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

.intro {
  height: 100vh;
  width: 100vw;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 10000;
  background: #fff;
  transition: all 300ms;
  opacity: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}

.intro video {
  position: absolute;
  height: 100%;
  width: 177.77777778vh; /* 100 * 16 / 9 */
  min-width: 100%;
  min-height: 56.25vw; /* 100 * 9 / 16 */
  top: 0;
  left: 0;
  z-index: 0;
  display: none;
}
@media screen and (min-width: 900px) {
  .intro video {
    display: block;
  }
}

.intro__container {
  position: relative;
  background: rgba(255, 255, 255, 0.9);
  padding: 2rem 4rem;
  text-align: center;
}
@media screen and (min-width: 900px) {
  .intro__container {
    margin: auto;
    border: solid 2.5px #343434;
  }
}

.intro--hidden {
  opacity: 0;
}

.intro h1 {
  text-transform: uppercase;
  font-weight: 700;
  letter-spacing: 0.3rem;
  border-bottom: solid 1px;
  margin-bottom: 2rem;
  background-color: #f1f1f1;
  color: #343434;
  border: solid 2.5px #343434;
  padding: 0.5rem 1.5rem;
  text-align: center;
  white-space: nowrap;
  font-size: 1.6rem;
  display: inline-block;
}

.intro__description {
  max-width: 280px;
  margin: 0 auto;
  text-align: left;
}
@media screen and (min-width: 900px) {
  .intro__description {
    max-width: 500px;
  }
}

.intro__description p {
  margin-bottom: 2rem;
}

.intro .btn {
  cursor: pointer;
  background: #343434;
  color: #fff;
  padding: 0.75rem 1.5rem;
  text-transform: uppercase;
  letter-spacing: 0.15rem;
  font-size: 0.75rem;
  display: inline-block;
  transition: all 400ms ease;
}
.intro .btn:hover {
  box-shadow: 0 5px 10px rgba(50, 50, 50, 0.2);
}
</style>
