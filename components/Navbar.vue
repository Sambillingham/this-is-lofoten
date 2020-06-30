<template>
  <div>
    <div @click="navIsOpen = !navIsOpen" class="nav-icon">
      <span></span>
      <span></span>
      <span></span>
    </div>
    <div :class="{ 'nav-list--active': navIsOpen }" class="nav-list">
      <h1>This is Lofoten</h1>
      <h2>Categories</h2>
      <div class="categories">
        <div
          v-for="category in navGroups"
          :key="category.name"
          class="category"
        >
          {{ category.name }}
        </div>
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
  z-index: 10300;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  cursor: pointer;
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
  z-index: 1000;
  background: #fff;
  width: 100%;
  text-align: center;
  transform: translateY(-100%);
  transition: 200ms ease-in-out;
}

.nav-list--active {
  transform: translateY(0);
}

.nav-list h1 {
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
.nav-list h2 {
  font-weight: 500;
  letter-spacing: 0.01rem;
  padding: 0.5rem;
  font-weight: 600;
}
.categories {
  padding: 0 1rem 1rem;
}

.category {
  padding: 0.25rem;
  cursor: pointer;
}
</style>
