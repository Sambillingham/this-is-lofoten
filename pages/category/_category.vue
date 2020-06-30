<template>
  <div>
    <Navbar />
    <h2>{{ category }}</h2>
    <div class="content">
      <div v-for="video in currentContent" :key="video" class="video">
        <a :href="`https://www.youtube.com/watch?v=${video.videoID}`">
          <img src="http://placehold.it/1280x720" />
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
    }
  },

  computed: {
    currentContent() {
      return videoData.filter((v) => {
        return v.categories.includes(this.category)
      })
    },
  },
}
</script>

<style scoped>
.content {
  display: flex;
  flex-direction: column;
  padding: 2rem;
}

.video {
  margin-bottom: 2rem;
  border-radius: 4px;
  box-shadow: 0 3px 3px rgba(0, 0, 0, 0.11);
}
.video h2 {
  margin: 0;
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
</style>
