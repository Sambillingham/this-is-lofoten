<template>
  <div class="category-container">
    <Navbar />
    <h2 class="content-title">{{ category }}</h2>
    <div class="content">
      <div v-for="video in currentContent" :key="video" class="video">
        <a :href="`https://www.youtube.com/watch?v=${video.videoID}`">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            version="1.1"
            viewBox="0 0 96 96"
            x="0px"
            y="0px"
          >
            <path
              d="M48 4c24.256 0 44 19.74 44 44s-19.744 44-44 44-44-19.74-44-44 19.744-44 44-44zM48 0c-26.508 0-48 21.492-48 48s21.492 48 48 48 48-21.492 48-48-21.492-48-48-48v0z"
              fill="#000000"
            />
            <path
              d="M34 70.784c-0.344 0-0.692-0.088-1-0.268-0.616-0.356-1-1.016-1-1.732v-41.576c0-0.716 0.384-1.376 1-1.732 0.616-0.36 1.384-0.36 2 0l36 20.784c0.616 0.356 1 1.016 1 1.732s-0.384 1.376-1 1.732l-36 20.784c-0.308 0.188-0.656 0.276-1 0.276zM36 30.68v34.64l30-17.32-30-17.32z"
              fill="#000000"
            />
          </svg>
          <img
            v-if="isMobile"
            :src="require(`~/assets/img/${video.thumbnail}`)"
          />
          <div v-else class="iframe-container">
            <iframe
              :src="`https://www.youtube.com/embed/${video.videoID}`"
              class="mb-4"
              frameborder="0"
              allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
              allowfullscreen
            ></iframe>
          </div>
          <h2>{{ video.title }}</h2>
        </a>
      </div>
    </div>
    <img :src="require('~/assets/img/contours-blue.svg')" class="contours-bg" />
  </div>
</template>

<script>
import Navbar from '@/components/Navbar'

import { videoData } from '@/assets/js/data'

export default {
  components: {
    Navbar,
  },

  data() {
    return {
      category: this.$route.params.category,
      windowWidth: window.innerWidth,
    }
  },

  computed: {
    currentContent() {
      return videoData.filter((v) => {
        return v.categories.includes(this.category)
      })
    },
    isMobile() {
      return this.windowWidth <= 900
    },
  },

  mounted() {
    window.addEventListener('resize', () => {
      return (this.windowWidth = window.innerWidth)
    })
  },
}
</script>

<style scoped>
.category-container {
  overflow: hidden;
  position: relative;
}

.contours-bg {
  position: fixed;
  opacity: 0.2;
  top: 0;
  left: 0;
  max-width: 1400px;
  width: 1400px;
  transform: translate(-602px, 407px) rotate(-46deg);
  z-index: 0;
}

@media screen and (min-width: 900px) {
  .contours-bg {
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

.content {
  display: flex;
  flex-direction: column;
  padding: 2rem;
  position: relative;
  z-index: 1;
}

.content-title {
  line-height: 1.8rem;
}

@media screen and (min-width: 900px) {
  .content-title {
    padding-left: 330px;
  }
  .content {
    padding-left: 330px;
  }
}

.video {
  display: flex;
  margin-bottom: 2rem;
  border-radius: 4px;
  position: relative;
}
.video img {
  border-radius: 15px;
  box-shadow: 0 3px 3px rgba(0, 0, 0, 0.11);
}

.video a {
  width: 100%;
  display: flex;
  flex-direction: column;
}
.video h2 {
  margin: -1rem 0 0;
  flex-grow: 1;
  box-shadow: 0 3px 3px rgba(0, 0, 0, 0.11);
  background: #e6e8ec;
  color: #2a4082;
}

.video svg {
  position: absolute;
  top: 3rem;
  left: 50%;
  width: 2.5rem;
  opacity: 0.6;
  transform: translateX(-50%);
}

.video svg path {
  fill: #fff;
}

@media screen and (min-width: 900px) {
  .content {
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: space-between;
  }
  .video {
    flex-basis: 48%;
  }
}

h2 {
  text-align: center;
  text-transform: uppercase;
  font-weight: 600;
  letter-spacing: 0.15rem;
  padding: 1rem;
  font-size: 0.725rem;
  background-color: #fafafa;
  box-shadow: 0 3px 3px #2a40821f;
  background-color: #2a4082;
  color: #fff;
  margin: 0.35rem;
  border-radius: 15px;
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
