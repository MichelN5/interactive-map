<template>
  <header class="topbar">
    <button class="menu-toggle" @click="toggleSidebar" v-if="isMobile">☰</button>
    <div class="logo" v-if="!isMobile">MyMap</div> 
    <div class="search-bar">
      <input 
        v-model="searchQuery" 
        type="text" 
        placeholder="Search for locations..." 
        @keyup.enter="searchLocation" 
        @input="updateSuggestions" 
      />
      <button @click="searchLocation">Search</button>
      <div class="dropdown" v-if="suggestions.length">
        <ul class="dropdown-menu">
          <li 
            v-for="(suggestion, index) in suggestions" 
            :key="index" 
            @click="selectSuggestion(suggestion.name)"
          >
            <span v-html="highlightMatch(suggestion.name)"></span> 
            <small class="category">{{ suggestion.category }}</small>
          </li>
        </ul>
      </div>
    </div>
  </header>
</template>

<script>
import mitt from "mitt";
import { locations } from "../locations.js"; // Correct import

const emitter = mitt();

export default {
  name: "MapTopbar",
  data() {
    return {
      isMobile: false,
      searchQuery: "",
      suggestions: [],
    };
  },
  mounted() {
    this.checkScreenSize();
    window.addEventListener("resize", this.checkScreenSize);
  },
  beforeUnmount() {
    window.removeEventListener("resize", this.checkScreenSize);
  },
  methods: {
    checkScreenSize() {
      this.isMobile = window.innerWidth <= 768;
    },
    toggleSidebar() {
      emitter.emit("toggle-sidebar");
    },
    searchLocation() {
      if (this.searchQuery.trim() !== "") {
        emitter.emit("search-location", this.searchQuery.trim());
      }
    },
    updateSuggestions() {
      const query = this.searchQuery.toLowerCase();
      this.suggestions = [];

      for (const [category, places] of Object.entries(locations)) {
        for (const placeName of Object.keys(places)) {
          if (placeName.toLowerCase().includes(query)) {
            this.suggestions.push({ name: placeName, category });
          }
        }
      }
    },
    selectSuggestion(suggestion) {
      this.searchQuery = suggestion;
      this.suggestions = [];
      this.searchLocation();
    },
    highlightMatch(suggestion) {
      const query = this.searchQuery.toLowerCase();
      const matchIndex = suggestion.toLowerCase().indexOf(query);
      if (matchIndex === -1) return suggestion;
      
      return (
        suggestion.slice(0, matchIndex) +
        "<strong>" +
        suggestion.slice(matchIndex, matchIndex + query.length) +
        "</strong>" +
        suggestion.slice(matchIndex + query.length)
      );
    },
  },
};

export { emitter };
</script>

<style scoped>
.topbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #2c3e50;
  color: #f3f4f6;
  padding: 15px 2.5%;
  width: 95%;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 3000;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.logo {
  font-size: 28px;
  font-weight: bold;
}

.search-bar {
  display: flex;
  align-items: center;
  position: relative;
  max-width: 300px;
  width: 100%;
}

.search-bar input {
  flex: 1;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px 0 0 4px;
  outline: none;
}

.search-bar button {
  padding: 8px 12px;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 0 4px 4px 0;
  cursor: pointer;
  transition: 0.3s;
  white-space: nowrap;
}

.search-bar button:hover {
  background-color: #2980b9;
}

.menu-toggle {
  display: none;
  font-size: 30px;
  background: none;
  border: none;
  color: white;
  cursor: pointer;
}

@media screen and (max-width: 768px) {
  .menu-toggle {
    display: block;
  }
}

.dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  background: white;
  color: black;
  border: 1px solid #ccc;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  z-index: 1000;
}

.dropdown-menu {
  list-style: none;
  padding: 0;
  margin: 0;
  max-height: 150px;
  overflow-y: auto;
}

.dropdown-menu li {
  padding: 8px;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.dropdown-menu li:hover {
  background: #f0f0f0;
}

.category {
  font-size: 12px;
  color: gray;
  margin-left: 8px;
}
</style>
