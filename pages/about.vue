<template>
  <div>
    <Navbar />
    <h2 class="content-title">About</h2>
    <div class="content">
      <p>
        This is Lofoten is a project made locally by the Lofoten Film
        Collective.
      </p>
      <p>
        We want to share our beautiful surroundings with the rest of the world,
        and we know that so many people wanted to visit Lofoten this year, but
        are unable to travel because of Covid-19. So here it is; now you can
        start dreaming, -and visit later! Use the map or categories to find the
        place or feeling you are looking for. Everything is uploaded on youtube,
        and you can find the playlists there as well.
      </p>
      <p>At least in real life. So let’s make reality virtual!</p>
      <p>
        By using the map, you can now experience the Lofoten Islands in 360° VR.
        Choose the location or feeling you want.
      </p>

      <p class="hashtag">Dream now - visit later.</p>

      <p>
        This is Lofoten works on all platforms, but we recommend using Chrome
        (link) on computers to get 8K full resolution, and the youtube app
        (link) on your smartphone. So far the playlists on Youtube would work
        best if you are on Oculus VR headset. Everything is uploaded on youtube,
        and you can find the playlists there as well.
      </p>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar'

export default {
  components: {
    Navbar,
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

@media screen and (min-width: 900px) {
  .content-title {
    padding-left: 330px;
    text-align: left;
  }
  .content {
    padding-left: 330px;
    flex-wrap: wrap;
    justify-content: space-between;
  }
}
</style>
