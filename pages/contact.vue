<template>
  <div>
    <Navbar />
    <h2 class="content-title">Contact</h2>
    <div class="content">
      <address>
        www.lofotenfilm.com<br />
        Lofoten Film Collective<br />
        Sjøgata 3<br />
        8310 Kabelvåg<br />
        Norway<br />
      </address>

      <p>
        Email: <a href="matilto:post@lofotenfilm.com">post@lofotenfilm.com</a>
      </p>

      <p>
        Facebook:
        <a href="https://facebook.com/lofotenfilmcollective"
          >www.facebook.com/lofotenfilmcollective</a
        >
      </p>
      <p>
        Instagram:
        <a href="https://instagram.com/lofotenfilm"
          >www.instagram.com/lofotenfilm</a
        >
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
  margin-bottom: 1rem;
  font-size: 1.1rem;
}
.content > address {
  margin-bottom: 2rem;
}

.content a {
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
