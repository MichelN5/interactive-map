<template>
  <div id="app">
    <MapTopbar />
    <div class="content-wrapper">
      <MapSidebar />
      <div class="map-container" id="map">
        <MapView />
      </div>
    </div>
  </div>
</template>

<script>
import MapTopbar from './components/MapTopbar.vue';
import MapSidebar from './components/MapSidebar.vue';
import MapView from './components/MapView.vue';

export default {
  components: {
    MapTopbar,
    MapSidebar,
    MapView,
  },
};
</script>

<style>

body{
  background-color: beige;
}
.content-wrapper {
  display: flex;
  margin-top: 80px; /* Space for the fixed topbar */
 
}

.map-container {
  margin-left: 340px; /* Adjust to account for the sidebar width */
  width: calc(100% - 340px);
  height: 100vh;
}

@media screen and (max-width: 768px) {
  .map-container{
    margin-left: 0px;
   width: 100%;
  }

}
</style>
