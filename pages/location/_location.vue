<template>
  <div></div>
</template>

<script>
import { videoData } from '@/assets/js/data'

export default {
  data() {
    return {
      vid: this.$route.params.location,
    }
  },

  computed: {
    content() {
      return videoData.find((v) => v.videoID === this.vid)
    },
    isMobile() {
      return this.windowWidth <= 900
    },
  },

  mounted() {
    this.$router.replace({
      path: '/',
      query: { 'vid': this.vid },
    })
  },

  head() {
    return {
      title: this.content.title,
      meta: [
        // hid is used as unique identifier. Do not use `vmid` for it as it will not work
        {
          hid: this.vid,
          name: this.content.title,
          content: this.content.description,
        },
      ],
    }
  },
}
</script>
