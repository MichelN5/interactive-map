<template>
  <aside class="sidebar" :class="{ 'sidebar-hidden': !isVisible }">
    <h2>Categories</h2>
    <div v-for="(places, category) in locations" :key="category" class="category">
      <button class="category-button" @click="toggleDropdown(category)">{{ category }}</button>
      <ul class="dropdown" v-show="dropdowns[category]">
        <li 
          v-for="(coords, placeName) in places" 
          :key="placeName" 
          class="category-item" 
          @click="goToLocation(placeName)"
        >
          {{ placeName }}
        </li>
      </ul>
    </div>
  </aside>
</template>

<script>
import { emitter } from './MapTopbar.vue';
import { locations } from '../locations.js';

export default {
  name: "MapSidebar",
  data() {
    return {
      dropdowns: {},
      isVisible: true, // To control visibility of sidebar on mobile
      locations: {},
    };
  },
  methods: {
    toggleDropdown(category) {
  this.dropdowns[category] = !this.dropdowns[category];
},

    toggleSidebar() {
      this.isVisible = !this.isVisible;
    },
    goToLocation(location) {
      emitter.emit('search-location', location);
      if (window.innerWidth <= 768) {
        this.isVisible = false;
      }
    },
  },
  created() {
    this.locations = locations;
    emitter.on('toggle-sidebar', this.toggleSidebar);
  },
  beforeUnmount() {
    emitter.off('toggle-sidebar', this.toggleSidebar);
  }
};
</script>

<style scoped>
.sidebar {
  padding: 20px;
  background-color: #2c3e50;
  color: #ffffff;
  width: 300px;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
  box-shadow: 2px 0 10px rgba(0, 0, 0, 0.2);
  overflow-y: auto;
  transition: transform 0.3s ease-in-out;
  z-index: 2000;
}

.sidebar-hidden {
  transform: translateX(-100%);
}

h2 {
  margin-top: 20%;
  font-size: 32px;
  margin-bottom: 20px;
  margin-left: 25%;
}

.category {
  margin-bottom: 30px;
}

.category-button {
  background-color: #3498db;
  color: white;
  border: none;
  padding: 12px 18px;
  margin: 0;
  border-radius: 6px;
  cursor: pointer;
  width: 100%;
  text-align: left;
  font-size: 18px;
  transition: background-color 0.3s;
}

.category-button:hover {
  background-color: #2980b9;
}

.dropdown {
  list-style: none;
  padding: 0;
  margin: 10px 0 0 0;
  background-color: #34495e;
  border-radius: 6px;
}

.dropdown li {
  margin: 8px 0;
  cursor: pointer;
  transition: color 0.3s;
}

.dropdown li:hover {
  color: #ecf0f1;
}

.category-item {
  background-color: #2c3e50;
  border-radius: 6px;
  padding: 15px;
  margin: 10px 0;
  font-size: 20px;
  transition: transform 0.3s, box-shadow 0.3s;
}

.category-item:hover {
  transform: translateY(-8px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.1);
}

@media screen and (max-width: 768px) {
  .sidebar {
    width: 250px; /* Adjust for mobile */
  }
}
</style>