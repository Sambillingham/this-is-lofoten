<template>
  <div>
    <Navbar />
    <h2 class="content-title">{{ category }}</h2>
    <div class="content">
      <div v-for="video in currentContent" :key="video" class="video">
        <a :href="`https://www.youtube.com/watch?v=${video.videoID}`">
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
.content {
  display: flex;
  flex-direction: column;
  padding: 2rem;
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
  box-shadow: 0 3px 3px rgba(0, 0, 0, 0.11);
}
.video a {
  width: 100%;
  display: flex;
  flex-direction: column;
}
.video h2 {
  margin: 0;
  flex-grow: 1;
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
  letter-spacing: 0.1rem;
  padding: 1rem;
  border-bottom: solid 1px;
  margin-bottom: 1rem;
  font-size: 0.875rem;
  background-color: #fafafa;
  box-shadow: 0 3px 3px rgba(0, 0, 0, 0.1);
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
