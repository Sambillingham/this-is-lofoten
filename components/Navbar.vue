<template>
  <div>
    <div @click="navIsOpen = !navIsOpen" class="nav-icon">
      <span></span>
      <span></span>
      <span></span>
    </div>
    <div :class="{ 'nav-list--active': navIsOpen }" class="nav-list">
      <header v-if="!isMobile">
        <img :src="require('~/assets/img/nav-header.svg')" class="nav-bg" />
        <h1><span class="l1">This is</span><span class="l2"> Lofoten</span></h1>
      </header>
      <nuxt-link to="/">
        <h2>Map</h2>
      </nuxt-link>
      <nuxt-link to="/about">
        <h2>About</h2>
      </nuxt-link>
      <nuxt-link to="/contact">
        <h2>Contact</h2>
      </nuxt-link>
      <h2>Categories</h2>
      <div class="categories">
        <nuxt-link
          :to="`/category/${category.name}`"
          v-for="category in navGroups"
          :key="category.name"
          class="category"
        >
          {{ category.name }}
        </nuxt-link>
      </div>
      <img
        :src="require('~/assets/img/contours-nav.svg')"
        class="contours-bg"
      />
    </div>
  </div>
</template>

<script>
import { videoData, categories } from '@/assets/js/data'

export default {
  components: {},

  data() {
    return {
      navIsOpen: false,
      windowWidth: window.innerWidth,
    }
  },

  computed: {
    isMobile() {
      return this.windowWidth <= 900
    },

    isHomePage() {
      return this.$route.path === '/'
    },

    navGroups() {
      return categories.map((cat) => {
        return {
          name: cat,
          list: videoData.filter((v) => v.categories.includes(cat)),
        }
      })
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
.nav-bg {
  position: absolute;
  top: -9rem;
  max-width: 434px;
  width: 2000px;
  transform: rotate(63deg) translate(6px, 98px);
}

.nav-icon {
  position: fixed;
  top: 0.35rem;
  left: 0.35rem;
  width: 3.3rem;
  height: 3.3rem;
  background: #2a4082;
  z-index: 130;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  cursor: pointer;
  border-radius: 15px;
  z-index: 1000;
}

@media screen and (min-width: 900px) {
  .nav-icon {
    display: none;
  }
}

.nav-icon span {
  height: 3px;
  width: 100%;
  background-color: #fafafa;
  display: inline-block;
}

.nav-list {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 100;
  background: #fff;
  transform: translateX(-100%);
  transition: 200ms ease-in-out;
  padding: 1.5rem 3rem 0.5rem 2rem;
  margin-top: 4.3rem;
  border-radius: 15px;
  overflow: hidden;
  color: #2a4082;
  z-index: 1000;
}

@media screen and (min-width: 900px) {
  .nav-list {
    margin-top: 0;
    width: 290px;
    transform: translateX(0);
    box-shadow: 0 3px 3px rgba(33, 33, 33, 0.1);
    padding: 0 0 0 2rem;
    border-radius: 0;
    padding-top: 8rem;
  }
}

.nav-list--active {
  transform: translateX(0.35rem);
  box-shadow: 0 10px 10px rgba(33, 33, 33, 0.2);
}

.nav-list h1 {
  text-transform: uppercase;
  font-weight: 700;
  letter-spacing: 0.2rem;
  padding: 1rem;
  border-bottom: solid 1px;
  margin-bottom: 1rem;
  background-color: #ffe3e3;
  color: #2a4082;
  position: absolute;
  border-radius: 15px;
  border: none;
  top: 2rem;
  padding: 0.5rem 1rem;
  margin-top: 0;
  text-align: center;
  white-space: nowrap;
  font-size: 1.1rem;
  display: inline-block;
}
.l1 {
  letter-spacing: 0.22rem;
}
.l2 {
  letter-spacing: 0.22rem;
}

.nav-list h2 {
  font-weight: 500;
  letter-spacing: 0.05rem;
  padding: 0.25rem 0.5rem 0.25rem 0;
  font-weight: 600;
  text-transform: uppercase;
  z-index: 1;
  position: relative;
}
@media screen and (min-width: 900px) {
  .nav-list h2 {
    padding: 0.5rem 0.5rem 0.5rem 0;
  }
}
.categories {
  padding: 0 1rem 1rem;
  z-index: 1;
  position: relative;
}

.category {
  padding: 0.18rem 0;
  cursor: pointer;
  display: block;
}

.contours-bg {
  position: absolute;
  opacity: 0.1;
  top: 0;
  left: 0;
  max-width: 1802px;
  width: 1861px;
  transform: translate(-667px, 37px) rotate(304deg);
  z-index: 0;
}

@media screen and (min-width: 900px) {
  .contours-bg {
    transform: translate(-651px, -130px) rotate(301deg);
    opacity: 0.125;
  }
}
</style>
