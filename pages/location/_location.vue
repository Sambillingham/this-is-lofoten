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
  },

  created() {
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
          hid: 'description',
          name: 'description',
          content: this.content.description,
        },
        {
          hid: 'og:title',
          property: 'og:title',
          content: this.content.title,
        },
        {
          hid: 'og:description',
          property: 'og:description',
          content: this.content.description,
        },
        {
          hid: 'og:url',
          property: 'og:url',
          content: 'https://thisislofoten.com' + this.$route.path,
        },
        {
          hid: 'og:image',
          property: 'og:image',
          content: `https://thisislofoten.com${require(`~/assets/img/${this.content.thumbnail}`)}`,
        },
      ],
    }
  },
}
</script>
