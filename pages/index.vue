<template>
  <div class="page">
    <div
      v-if="initialLoad"
      class="intro"
      :class="{ 'intro--hidden': !initialLoad }"
    >
      <video
        v-if="!isMobile"
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
        <header>
          <img
            :src="require('~/assets/img/nav-header.svg')"
            class="intro-header-bg"
          />
          <h1>Welcome To This Is Lofoten</h1>
        </header>
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
        <span class="btn rounded-full" @click="hideIntro()">
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
    <div
      v-if="!initialLoad"
      :class="{ 'drawer--is-active': drawerOpen }"
      class="drawer"
    >
      <div v-if="drawerOpen" class="contours-container">
        <h3 class="drawerCoords mb-1 text-xs leading-tight capitalize">
          {{ drawerCoords }}
        </h3>
        <h2
          class="mb-4 text-xl xs:font-bold sm:font-bold md:text-2xl text-gray-900 leading-tight capitalize"
        >
          {{ drawerTitle }}
        </h2>
        <div
          v-html="drawerDescription"
          class="mb-8 text-base text-gray-900 leading-normal relative z-10"
        ></div>
        <div class="mb-2 tags">
          <nuxt-link
            class="mr-2 text-xs"
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
        <div v-if="!isMobile" class="iframe-container mb-2">
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
          v-if="isMobile"
          :href="`https://www.youtube.com/watch?v=${drawerVideoID}`"
          class="w-auto rounded-full block bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 text-center mb-2"
          >Watch Now!</a
        >
        <a
          target="_blank"
          v-if="drawerOculusLink"
          class="w-50 block bg-gray-500 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded-full text-center mb-2"
          :href="drawerOculusLink"
        >
          Save To Oculus
        </a>
        <a
          :href="
            `https://www.facebook.com/sharer/sharer.php?u=https://thisislofoten.com/location/${drawerVideoID}`
          "
          onclick="javascript:window.open(this.href, 'Facebook Sharing', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=800,width=500');return false;"
          target="_blank"
          title="Share on Facebook"
          class="w-50 block text-white font-bold py-2 px-4 rounded-full text-center fb-btn mb-2"
        >
          <svg
            height="24px"
            width="24px"
            role="img"
            viewBox="0 0 24 24"
            xmlns="http://www.w3.org/2000/svg"
          >
            <title>Facebook icon</title>
            <path
              d="M23.9981 11.9991C23.9981 5.37216 18.626 0 11.9991 0C5.37216 0 0 5.37216 0 11.9991C0 17.9882 4.38789 22.9522 10.1242 23.8524V15.4676H7.07758V11.9991H10.1242V9.35553C10.1242 6.34826 11.9156 4.68714 14.6564 4.68714C15.9692 4.68714 17.3424 4.92149 17.3424 4.92149V7.87439H15.8294C14.3388 7.87439 13.8739 8.79933 13.8739 9.74824V11.9991H17.2018L16.6698 15.4676H13.8739V23.8524C19.6103 22.9522 23.9981 17.9882 23.9981 11.9991Z"
            />
          </svg>
          Share on Facebook
        </a>
        <span @click="closeDrawer()" class="close cursor-pointer">&times;</span>
      </div>
      <img
        v-if="drawerOpen"
        :src="require('~/assets/img/contours-drawer.svg')"
        class="contours-bg-drawer"
      />
    </div>
  </div>
</template>

<script>
import mapboxgl from 'mapbox-gl'

import { videoData, labelData } from '@/assets/js/data'

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
      initialLoad: false,
      animateOutIntro: false,
      drawerCategories: [],
      drawerOculusLink: '',
      initialOpen: this.$route.query.vid,
    }
  },

  computed: {
    isMobile() {
      return this.windowWidth <= 900
    },
    initialDrawerContent() {
      return videoData.find((v) => v.videoID === this.initialOpen)
    },
  },

  methods: {
    closeDrawer() {
      this.drawerOpen = false
      this.drawerTitle = ''
      this.drawerDescription = ''
      this.drawerThumbnail = ''
      this.drawerVideoID = ''
      this.drawerCoords = ''
      this.drawerCategories = []
      this.drawerOculusLink = ''
      history.pushState({}, null, `/`)
    },

    checkCookie() {
      if (
        !document.cookie
          .split('; ')
          .find((row) => row.startsWith('initialLoad'))
      ) {
        this.initialLoad = true
        document.cookie =
          'initialLoad=false; expires=Fri, 31 Dec 9999 23:59:59 GMT'
      }
    },
    hideIntro() {
      this.animateOutIntro = true
      setTimeout(() => {
        this.initialLoad = false
      }, 500)
    },

    autoLoadMapPanel(map) {
      this.drawerTitle = this.initialDrawerContent.title
      this.drawerDescription = this.initialDrawerContent.description
      this.drawerThumbnail = this.initialDrawerContent.thumbnail
      this.drawerVideoID = this.initialDrawerContent.videoID
      this.drawerOpen = true
      this.drawerCoords = `${this.initialDrawerContent.coordinates[1].toFixed(
        5,
      )}° N  ${this.initialDrawerContent.coordinates[0].toFixed(5)}° W`

      this.drawerCategories = this.initialDrawerContent.categories
      this.drawerOculusLink = this.initialDrawerContent.oculus || ''
      history.pushState(
        {},
        null,
        `/location/${this.initialDrawerContent.videoID}`,
      )
    },
  },

  mounted() {
    let passiveSupported = false

    try {
      const options = {
        get passive() {
          passiveSupported = true
          return false
        },
      }
      window.addEventListener('test', null, options)
      window.removeEventListener('test', null, options)
    } catch (err) {
      passiveSupported = false
    }

    window.addEventListener(
      'resize',
      () => {
        return (this.windowWidth = window.innerWidth)
      },
      passiveSupported ? { passive: true } : false,
    )

    this.checkCookie()

    const app = this
    mapboxgl.accessToken = this.accessToken

    const map = new mapboxgl.Map({
      container: 'mapContainer',
      style: 'mapbox://styles/sbillinghammap/ckbqjqxcv51i31in0vqbxk82p',
      center: [13.9, 68.2],
      zoom: 6.2,
      maxBounds: [
        [11.2, 66.6],
        [16, 69.5],
      ],
    })

    const nav = new mapboxgl.NavigationControl()

    if (!this.isMobile) {
      map.addControl(nav, 'top-right')
    }

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
            'text-font': ['DIN Offc Pro Medium'],
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
            'circle-color': '#2a4082',
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
            'text-font': ['DIN Offc Pro Medium'],
            'text-size': 17,
            'text-offset': [0, 2],
          },
          paint: {
            'text-color': 'white',
          },
        })

        map.addSource('business', {
          type: 'geojson',
          data: {
            type: 'FeatureCollection',
            features: labelData.map((d) => {
              return {
                type: 'Feature',
                geometry: {
                  type: 'Point',
                  coordinates: d.coordinates,
                },
                properties: {
                  title: d.title,
                  facebook: d.facebook,
                },
              }
            }),
          },
        })

        map.addLayer({
          id: 'name-labels',
          type: 'symbol',
          source: 'business',
          minzoom: 9,
          maxzoom: 22,
          layout: {
            'text-field': ['get', 'title'],
            'text-font': ['DIN Offc Pro Medium'],
            'text-size': 15,
            'text-offset': [0, 2.5],
            'text-allow-overlap': true,
          },
          paint: {
            'text-color': '#2a4082',
            'text-halo-color': 'white',
            'text-halo-blur': 3,
            'text-halo-width': 1,
          },
        })

        map.on('click', 'name-labels', function(e) {
          const features = map.queryRenderedFeatures(e.point, {
            layers: ['name-labels'],
          })

          if (!features.length) return

          const win = window.open(features[0].properties.facebook, '_blank')
          win.focus()
        })

        map.on('dragstart', function() {
          app.closeDrawer()
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

          const offset = app.isMobile ? [0, -220] : [-150, 0]

          map.flyTo({
            center: feature.geometry.coordinates,
            zoom: 11,
            speed: 1.5,
            offset: [...offset],
          })

          history.pushState({}, null, `/location/${feature.properties.videoID}`)
        })
        if (app.initialOpen) {
          app.autoLoadMapPanel()

          const offset = app.isMobile ? [0, -220] : [-150, 0]

          console.log(app.initialDrawerContent.coordinates)
          map.flyTo({
            center: app.initialDrawerContent.coordinates,
            zoom: 11,
            speed: 1.5,
            offset: [...offset],
          })
        }
      }) // load pin image
    }) // end map load
  },
}
</script>

<style scoped>
.page {
  width: 100%;
  height: 100%;
  position: relative;
}

.map {
  position: fixed;
  width: 100%;
  height: 100vh;
  top: 0;
  left: 0;
}

.drawer {
  width: 100%;
  background: #fff;
  transform: translateY(100vh);
  transition-delay: 350ms;
  transition: all 350ms ease-in-out;
  display: flex;
  justify-content: flex-start;
  flex-direction: column;
  margin-top: 35vh;
  min-height: 0;
  border-radius: 15px;
  overflow: hidden;
  position: relative;
  z-index: 100;
  background: rgba(253, 251, 251, 1);
}

.drawer a {
  font-weight: 700;
}

.drawer--is-active {
  min-height: 65vh;
  transition: transform 350ms ease-in-out;
  transition-delay: 50ms;
  transform: translateY(0);
  box-shadow: 0 -5px 10px rgba(0, 0, 0, 0.15);
}

.contours-container {
  padding: 2rem;
  position: relative;
}

.close {
  background: #2a4082;
  color: #fff;
  border-radius: 50%;
  position: absolute;
  font-size: 1.3rem;
  text-align: center;
  width: 1.75rem;
  display: inline-block;
  top: 0.5rem;
  line-height: 1.8rem;
  right: 0.5rem;
  z-index: 1;
  font-family: monospace;
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
    height: 100%;
    max-height: 100%;
    position: fixed;
    border-top-right-radius: 0;
    border-bottom-right-radius: 0;
    margin-top: 0;
    justify-content: flex-end;
  }

  .drawer--is-active {
    transform: translate(0, 0);
    box-shadow: 0 0 15px rgba(0, 0, 0, 0.3);
  }

  .contours-container {
    position: static;
  }
  .close {
    left: 0.5rem;
    right: auto;
  }
}

.drawer a {
  position: relative;
  z-index: 1;
  font-weight: 700;
}
.drawer h2 {
  color: #2a4082;
  font-weight: 500;
}
.drawer img {
  border-radius: 5px;
  pointer-events: none;
}
.drawerCoords {
  background-color: #2a4082;
  color: #fafafa;
  padding: 0.2rem 0.3rem;
  border-radius: 5px;
  opacity: 0.4;
  font-size: 0.65rem;
  display: inline-block;
}
.tags a {
  background-color: #bdfff0;
  color: #2a4082;
  padding: 0.22rem;
  border-radius: 5px;
}

.mobile-banner {
  width: calc(100% - 0.7rem);
  /* top: 0.35rem; */
  position: fixed;
  /* left: 0.35rem; */
  z-index: 1;
  border-radius: 15px;
  text-align: center;
  text-transform: uppercase;
  font-weight: 600;
  letter-spacing: 0.1rem;
  padding: 1rem;
  font-size: 0.875rem;
  color: #fafafa;
  background: #2a4082;
  box-shadow: 0 3px 3px rgba(0, 0, 0, 0.1);
}

.iframe-container {
  overflow: hidden;
  padding-top: 56.25%;
  position: relative;
  z-index: 1;
  border-radius: 5px;
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
}

.intro header {
  overflow: hidden;
  position: relative;
  margin-bottom: 1rem;
  border-radius: 15px;
  background: #2a4082;
}

.intro-header-bg {
  transform: rotate(41deg) translate(-65px, 14px);
  width: 179%;
  max-width: 147%;
  position: absolute;
  top: -5rem;
  z-index: 0;
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

@media screen and (min-width: 360px) {
  .intro__container {
    max-height: 100vh;
  }
  .intro h1 {
    margin: 3rem 0.5rem 5rem;
  }
  .intro__description p {
    margin-bottom: 2rem;
  }
}

@media screen and (min-width: 900px) {
  .intro {
    align-items: center;
    overflow: hidden;
  }
  .intro header {
    padding: 0 0 2rem;
    background: transparent;
  }
  .intro video {
    display: block;
  }
  .intro-header-bg {
    transform: rotate(41deg) translate(-40px, 0px);
    top: -10rem;
  }
}

.intro__container {
  position: relative;
  background: rgba(255, 255, 255, 0.9);
  text-align: center;
  transition: all 350ms ease-in-out;
  border-radius: 15px;
  overflow: hidden;
  border: none;
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
  pointer-events: none;
}

@media screen and (min-width: 900px) {
  .intro__container {
    margin: auto;
    box-shadow: 0 5px 10px rgba(50, 50, 50, 0.2);
    border-radius: 15px;
    overflow: hidden;
    min-height: auto;
  }

  .contours-bg {
    max-width: 1000px;
    width: 1000px;
    opacity: 0.27;
    transform: translate(-237px, 51px) rotate(243deg);
  }
}

.contours-bg-drawer {
  position: absolute;
  opacity: 0.1;
  top: 0;
  left: 0;
  max-width: 1800px;
  width: 1861px;
  transform: translate(-752px, 0px) rotate(272deg);
  z-index: 0;
  pointer-events: none;
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
  letter-spacing: 0.18rem;
  padding: 1rem 2rem;
  text-align: center;
  white-space: nowrap;
  font-size: 0.75rem;
  display: inline-block;
  color: #2a4082;
  border-radius: 15px;
  background: #bdfff0;
  position: relative;
  margin: 1rem 0.5rem 1rem;
}

.intro__description {
  max-width: 280px;
  margin: 0 auto;
  text-align: left;
}

.intro__description p {
  margin-bottom: 1rem;
  font-size: 1.1rem;
  color: #2a4082;
}
@media screen and (min-width: 900px) {
  .intro h1 {
    font-size: 1.25rem;
    margin: 2rem 1rem 6rem;
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
  background: #2a4082;
  color: #bdfff0;
  padding: 0.75rem 1.5rem;
  text-transform: uppercase;
  letter-spacing: 0.15rem;
  font-size: 0.75rem;
  display: inline-block;
  transition: all 400ms ease;
  position: relative;
  z-index: 1;
  margin-bottom: 3rem;
}
.intro .btn:hover {
  background: #152965;
  box-shadow: 0 5px 10px rgba(50, 50, 50, 0.2);
}
.fb-btn {
  background: #1877f2;
  cursor: pointer;
  position: relative;
}

.fb-btn svg {
  fill: #fff;
  position: absolute;
  left: 0.75rem;
  top: 50%;
  transform: translateY(-50%);
}
</style>
