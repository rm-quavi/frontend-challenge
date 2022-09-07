<template>
  <b-container fluid class="wrapper">
    <b-row class="mx-0">
      <b-col cols="12" class="heading-container">
        <h1 class="heading">Load profiles</h1>
        <h5 class="subheading pl-3">Available datasets</h5>
      </b-col>

      <b-col class="navigation-container">
        <Sidebar 
          :profiles="profiles"
          @onProfileClick="selectedProfile = $event"
          :selectedProfile="selectedProfile"
        ></Sidebar>
      </b-col>

      <b-col class="chart-container">
        <Chart
          :loadProfile="selectedProfile"
        ></Chart>
      </b-col>
    </b-row>
  </b-container>
</template>

<script lang="ts">
import Vue from 'vue'
import Chart from '~/components/Chart.vue'
import Sidebar from '~/components/Sidebar.vue'
import profiles from '~/data/load-profiles.json'

export default Vue.extend({
  components: { Chart, Sidebar },
  data() {
    return {
      profiles: [],
      selectedProfile: {},
    }
  },
  created() {
    this.profiles = profiles as []
    this.selectedProfile = this.profiles[0]
  },
})
</script>

<style lang="scss" scoped>
.wrapper {
  background-color: #102D4C;
  min-height: 100vh;
  padding: 20px;
  margin: 0;

  .chart-container,
  .navigation-container {
    background-color: #F1F1F1;
    padding: 0;
    margin: 0;
  }

  .navigation-container {
    width: 100%;
    max-width: 100%;
  }

  .heading-container > * {
    color: white;
    display: inline-block;
  }
}

@media screen and (min-width: 768px) {
  .wrapper {
    .navigation-container {
      width: 300px;
      max-width: 300px;
    }
  }
}
</style>
