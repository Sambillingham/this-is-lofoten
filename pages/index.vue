<template>
  <div>
    <div
      v-if="initialLoad"
      class="intro"
      :class="{ 'intro--hidden': !initialLoad }"
    >
      <video
        width="100%"
        height="100%"
        loop
        autoplay
        muted
        :class="{ 'video--hidden': animateOutIntro }"
      >
        <source :src="require(`~/assets/video/bg.mp4`)" type="video/mp4" />
      </video>
      <div
        class="intro__container"
        :class="{ 'intro__container--hidden': animateOutIntro }"
      >
        <header><h1>Welcome To This Is Lofoten</h1></header>
        <div class="intro__description">
          <p>
            Immerse yourself in the beauty of the Lofoten Islands in 360°
            virtual reality.
          </p>
          <p>
            Discover the area through the map and choose your favourite place,
            activity or season.
          </p>
          <p class="hashtag">#dreamnowvisitlater</p>
        </div>
        <span class="btn" @click="hideIntro()">
          Continue
        </span>
        <img :src="require('~/assets/img/contours.svg')" class="contours-bg" />
      </div>
    </div>

    <Navbar />
    <div v-if="isMobile" class="mobile-banner">
      This Is Lofoten
    </div>
    <div id="mapContainer" class="map"></div>
    <div :class="{ 'drawer--is-active': drawerOpen }" class="drawer fixed">
      <h3 class="mb-1 text-xs text-gray-600 leading-tight capitalize">
        {{ drawerCoords }}
      </h3>
      <h2
        class="mb-4 xs:text-xl md:text-2xl text-gray-900 leading-tight capitalize"
      >
        {{ drawerTitle }}
      </h2>
      <div
        v-if="!isMobile"
        v-html="drawerDescription"
        class="mb-8 text-base text-gray-900 leading-normal relative z-10"
      ></div>
      <div class="mb-4">
        <nuxt-link
          class="mr-4 text-xs"
          v-for="cat in drawerCategories"
          :key="cat"
          :to="`/category/${cat}`"
          >#{{ cat }}</nuxt-link
        >
      </div>
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
      <div v-if="!isMobile" class="iframe-container mb-4">
        <iframe
          :src="`https://www.youtube.com/embed/${drawerVideoID}`"
          width="100%"
          height="400px"
          frameborder="0"
          allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
          allowfullscreen
        ></iframe>
      </div>
      <a
        target="_blank"
        v-if="drawerOculusLink"
        class="w-50 block bg-gray-500 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded text-center mb-2"
        :href="drawerOculusLink"
      >
        Save To Oculus
      </a>
      <a
        v-if="isMobile"
        :href="`https://www.youtube.com/watch?v=${drawerVideoID}`"
        class="w-auto block bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded text-center"
        >Watch Now!</a
      >
      <span @click="drawerOpen = false" class="close cursor-pointer"
        >&times;</span
      >
      <img
        :src="require('~/assets/img/contours-drawer.svg')"
        class="contours-bg-drawer"
      />
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
      drawerCoords: '',
      windowWidth: window.innerWidth,
      initialLoad: true,
      animateOutIntro: false,
      drawerCategories: [],
      drawerOculusLink: '',
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
    hideIntro() {
      this.animateOutIntro = true
      setTimeout(() => {
        this.initialLoad = false
      }, 500)
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
        [11, 66.6],
        [16, 69.5],
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
                  short_title: v.short_title,
                  description: v.description,
                  videoID: v.videoID,
                  thumbnail: v.thumbnail,
                  categories: v.categories,
                  oculus: v.oculus,
                },
              }
            }),
          },
          cluster: true,
          clusterMaxZoom: 14, // Max zoom to cluster points on
          clusterRadius: 11, // Radius of each cluster when clustering points (defaults to 50)
        })

        // disable map rotation using right click + drag
        map.dragRotate.disable()

        // disable map rotation using touch rotation gesture
        map.touchZoomRotate.disableRotation()

        map.addLayer({
          id: 'point-labels',
          type: 'symbol',
          source: 'point',
          minzoom: 9,
          maxzoom: 22,
          layout: {
            'text-field': ['get', 'short_title'],
            'text-font': ['DIN Offc Pro Medium', 'Arial Unicode MS Bold'],
            'text-size': 15,
            'text-offset': [0, 2.5],
            'text-allow-overlap': true,
          },
          paint: {
            'text-color': '#333333',
            'text-halo-color': 'white',
            'text-halo-blur': 3,
            'text-halo-width': 1,
          },
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

          const offset = app.isMobile ? [0, -75] : [-50, 0]

          map
            .getSource('point')
            .getClusterExpansionZoom(clusterId, function(err, zoom) {
              if (err) return
              map.easeTo({
                center: features[0].geometry.coordinates,
                zoom: zoom + 0.5,
                speed: 1.5,
                offset: [...offset],
              })
            })
        })

        map.on('click', 'points', function(e) {
          const features = map.queryRenderedFeatures(e.point, {
            layers: ['points'],
          })

          if (!features.length) {
            return
          }
          const feature = features[0]

          if (feature.properties.point_count > 1) {
            return map
              .getSource('point')
              .getClusterExpansionZoom(feature.properties.cluster_id, function(
                err,
                zoom,
              ) {
                if (err) return
                map.easeTo({
                  center: features[0].geometry.coordinates,
                  zoom: zoom + 0.5,
                  speed: 1.5,
                })
              })
          }

          app.drawerTitle = feature.properties.title
          app.drawerDescription = feature.properties.description
          app.drawerThumbnail = feature.properties.thumbnail
          app.drawerVideoID = feature.properties.videoID
          app.drawerOpen = true
          app.drawerCoords = `${feature.geometry.coordinates[1].toFixed(
            5,
          )}° N  ${feature.geometry.coordinates[0].toFixed(5)}° W`

          app.drawerCategories = JSON.parse(feature.properties.categories)
          app.drawerOculusLink = feature.properties.oculus || ''

          const offset = app.isMobile ? [0, -175] : [-150, 0]

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

<style scoped>
.map {
  position: fixed;
  width: 100%;
  height: calc(100% - 54px);
}

.drawer {
  bottom: 0;
  left: 0;
  width: 100%;
  background: #fff;
  padding: 2rem;
  transform: translateY(100vh);
  transition: 300ms ease-in-out;
  display: flex;
  justify-content: center;
  flex-direction: column;
  overflow: hidden;
}

.drawer a {
  font-weight: 700;
}

.drawer--is-active {
  transform: translateY(0);
  position: fixed;
  box-shadow: 0 -5px 10px rgba(0, 0, 0, 0.15);
}

@media screen and (min-width: 900px) {
  .map {
    height: 100%;
  }
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
    box-shadow: 0 0 15px rgba(0, 0, 0, 0.3);
  }
}

.drawer a {
  position: relative;
  z-index: 1;
  font-weight: 700;
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

.mobile-banner {
  text-align: center;
  text-transform: uppercase;
  font-weight: 600;
  letter-spacing: 0.1rem;
  padding: 1rem;
  border-bottom: solid 1px;
  font-size: 0.875rem;
  background-color: #fafafa;
  box-shadow: 0 3px 3px rgba(0, 0, 0, 0.1);
}

.close {
  position: absolute;
  padding: 1rem;
  font-size: 1.8rem;
  top: 0;
  right: 0;
  z-index: 1;
}
.iframe-container {
  overflow: hidden;
  padding-top: 56.25%;
  position: relative;
  z-index: 1;
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
  object-fit: cover;
  transition: opacity 450ms ease-in;
}

.video--hidden {
  opacity: 0;
}

@media screen and (min-width: 900px) {
  .intro video {
    display: block;
  }
  .intro {
    /* align-items: center; */
  }
}

.intro__container {
  position: relative;
  background: rgba(255, 255, 255, 0.9);
  text-align: center;
  transition: all 350ms ease-in-out;
}

.contours-bg {
  position: absolute;
  opacity: 0.2;
  top: 0;
  left: 0;
  max-width: 1800px;
  width: 1861px;
  transform: translate(-752px, 0px) rotate(272deg);
  z-index: 0;
}

@media screen and (min-width: 900px) {
  .intro__container {
    margin: auto;
    border: solid 2px #e8e8e8;
    box-shadow: 0 5px 10px rgba(50, 50, 50, 0.2);
    border-radius: 4px;
    padding: 4rem;
    overflow: hidden;
  }

  .contours-bg {
    max-width: 1000px;
    width: 1000px;
    opacity: 0.27;
    transform: translate(-26px, -92px) rotate(175deg);
  }
}

.contours-bg-drawer {
  position: absolute;
  opacity: 0.2;
  top: 0;
  left: 0;
  max-width: 1800px;
  width: 1861px;
  transform: translate(-752px, 0px) rotate(272deg);
  z-index: 0;
}

@media screen and (min-width: 900px) {
  .contours-bg-drawer {
    position: absolute;
    opacity: 0.2;
    top: 0;
    left: 0;
    max-width: 1400px;
    width: 1400px;
    transform: translate(-352px, 300px) rotate(-15deg);
    z-index: 0;
  }
}

.intro__container--hidden {
  opacity: 0;
  transform: translateY(15px);
}

.intro--hidden {
  opacity: 0;
}

.intro h1 {
  text-transform: uppercase;
  font-weight: 700;
  letter-spacing: 0.11rem;
  border-bottom: solid 1px;
  margin-bottom: 2rem;
  color: #343434;
  border-bottom: solid 2.5px #343434;
  padding: 0.5rem 0;
  text-align: center;
  white-space: nowrap;
  font-size: 1.1rem;
  display: inline-block;
}

.intro__description {
  max-width: 280px;
  margin: 0 auto;
  text-align: left;
}

.intro__description p {
  margin-bottom: 2rem;
  font-size: 1.1rem;
}
@media screen and (min-width: 900px) {
  .intro h1 {
    text-transform: uppercase;
    font-weight: 700;
    letter-spacing: 0.18rem;
    border-bottom: solid 1px;
    margin-bottom: 2rem;
    color: #343434;
    border-bottom: solid 2.5px #343434;
    padding: 0.5rem 0;
    text-align: center;
    white-space: nowrap;
    font-size: 1.6rem;
    display: inline-block;
  }

  .intro__description {
    max-width: 360px;
    text-align: center;
  }

  .intro__description p {
    margin-bottom: 1.2rem;
    font-size: 1rem;
  }
}

.intro__description p.hashtag {
  font-weight: 700;
  text-align: center;
  text-align: center;
  letter-spacing: 0.03rem;
  font-style: italic;
  font-size: 1.2rem;
  position: relative;
  z-index: 1;
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
  border-radius: 4px;
  position: relative;
  z-index: 1;
}
.intro .btn:hover {
  background: #545454;
  box-shadow: 0 5px 10px rgba(50, 50, 50, 0.2);
}
</style>
