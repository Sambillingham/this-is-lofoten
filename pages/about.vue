<template>
  <div>
    <Navbar />
    <h2 class="content-title">About</h2>
    <div class="content">
      <p>
        This is Lofoten is a project made locally by the
        <a href="https://lofotenfilm.com">Lofoten Film Collective.</a>
      </p>
      <p>
        We want to share our beautiful surroundings with the rest of the world,
        and we know that so many people wanted to visit Lofoten this year, but
        are unable to travel because of Covid-19. So here it is; now you can
        start dreaming, -and visit later! Use the map or categories to find the
        place or feeling you are looking for. Everything is uploaded on youtube,
        and you can find the playlists there as well.
      </p>
      <p>
        We are constantly uploading new videos, from new places and new
        activities!
      </p>
      <p>
        By using the map, you can now experience the Lofoten Islands in 360° VR.
        Choose the location or feeling you want.
      </p>

      <p class="hashtag">Dream now - visit later.</p>

      <p>
        This is Lofoten works on all platforms, but we recommend using
        <a target="_blank" href="https://www.google.com/chrome/">Chrome</a>
        on computers to get 8K full resolution, and the youtube app via
        <a
          target="_blank"
          href="https://play.google.com/store/apps/details?id=com.google.android.youtube&hl=en"
        >
          Google Play (Android)</a
        >
        or
        <a
          target="_blank"
          href="https://apps.apple.com/us/app/youtube-watch-listen-stream/id544007664"
          >App Store (iOS)</a
        >
        on your smartphone. So far the playlists on Youtube would work best if
        you are on Oculus VR headset. Everything is uploaded on youtube, and you
        and you can find the playlists there as well.
      </p>
      <div>
        <h3>Supported By</h3>
        <div class="support">
          <img v-for="img in support" :src="img" />
        </div>
        <h3>In cooperation with</h3>
        <div class="support support--center">
          <a href="https://lofoten.info/Visitlofoten">
            <img :src="require('~/assets/img/visit-lofoten.png')" />
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar'

export default {
  components: {
    Navbar,
  },
  data() {
    return {
      support: [
        require('~/assets/img/inn-norge.jpg'),
        require('~/assets/img/vestvagoy-kommune-logo.png'),
        require('~/assets/img/vgan-kommune.png'),
      ],
    }
  },
}
</script>

<style scoped>
.content {
  display: flex;
  flex-direction: column;
  padding: 2rem;
}

.content > * {
  max-width: 600px;
  margin-bottom: 1rem;
  font-size: 1.1rem;
}
.hashtag {
  font-weight: 700;
  color: #2a4082;
  font-style: italic;
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
  background-color: #2a4082;
  color: #fff;
  margin: 0.35rem;
  border-radius: 15px;
}

a {
  font-weight: 700;
  color: #2a4082;
}

h3 {
  margin: 4rem 0 2rem;
  font-weight: 600;
  color: #2a4082;
  text-transform: uppercase;
  letter-spacing: 2px;
  text-align: center;
}

.support {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  flex-direction: column;
}
.support--center {
  justify-content: center;
}
.support img {
  max-width: 200px;
  max-width: 30%;
}

@media screen and (min-width: 900px) {
  .content-title {
    margin-left: 316px;
    text-align: left;
  }
  .content {
    padding-left: 330px;
    flex-wrap: wrap;
    justify-content: space-between;
  }
  .content a {
    font-size: 1.1rem;
  }
  h2 {
    margin: 0;
    border-radius: 0;
    border-bottom-left-radius: 15px;
  }
  .support {
    flex-direction: row;
  }
}
</style>
