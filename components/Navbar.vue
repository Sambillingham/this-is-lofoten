<template>
  <div>
    <div @click="navIsOpen = !navIsOpen" class="nav-icon">
      <span></span>
      <span></span>
      <span></span>
    </div>
    <div :class="{ 'nav-list--active': navIsOpen }" class="nav-list">
      <header>
        <h1><span class="l1">This is</span><span class="l2"> Lofoten</span></h1>
      </header>
      <nuxt-link to="/">
        <h2>Map</h2>
      </nuxt-link>
      <nuxt-link to="/">
        <h2>About</h2>
      </nuxt-link>
      <nuxt-link to="/">
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
    }
  },

  computed: {
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
}
</script>

<style>
.nav-icon {
  position: absolute;
  top: 0;
  left: 0;
  width: 3.3rem;
  height: 3.3rem;
  background: #fafafa;
  z-index: 130;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  cursor: pointer;
}

@media screen and (min-width: 900px) {
  .nav-icon {
    display: none;
  }
}

.nav-icon span {
  height: 3px;
  width: 100%;
  background-color: #222;
  display: inline-block;
}

.nav-list {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 100;
  background: #fff;
  width: 100%;
  transform: translateY(-100%);
  transition: 200ms ease-in-out;
  box-shadow: 0 3px 3px rgba(33, 33, 33, 0.1);
  padding: 0 3.5rem;
}

@media screen and (min-width: 900px) {
  .nav-list {
    width: 320px;
    transform: translateY(0);
    box-shadow: 0 3px 13px rgba(33, 33, 33, 0.1);
    padding: 0 3rem;
  }
}

.nav-list--active {
  transform: translateY(0);
}

.nav-list h1 {
  text-transform: uppercase;
  font-weight: 700;
  letter-spacing: 0.2rem;
  padding: 1rem;
  border-bottom: solid 1px;
  margin-bottom: 1rem;
  background-color: #f1f1f1;
  color: #343434;
  border: solid 2.5px #343434;
  padding: 0.5rem 1rem;
  margin-top: 3.5rem;
  text-align: center;
  white-space: nowrap;
  font-size: 1.1rem;
}
.l1 {
  letter-spacing: 0.22rem;
}
.l2 {
  letter-spacing: 0.22rem;
}

@media screen and (min-width: 900px) {
  .nav-list h1 {
    margin-top: 2rem;
  }
}

.nav-list h2 {
  font-weight: 500;
  letter-spacing: 0.05rem;
  padding: 0.5rem 0.5rem 0.5rem 0;
  font-weight: 600;
  text-transform: uppercase;
  /* font-size: 0.875rem; */
}
.categories {
  padding: 0 1rem 1rem;
}

.category {
  padding: 0.25rem;
  cursor: pointer;
  display: block;
}
</style>
