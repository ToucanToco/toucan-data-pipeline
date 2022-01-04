<template>
  <div class="app-data-pipeline">
    <h1>App data pipeline</h1>
    <Cytoscape layout="elk" class="app-data-pipeline__viz"></Cytoscape>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import Cytoscape from "@/components/Cytoscape.vue";

export default Vue.extend({
  name: "AppDataPipeline",

  components: {
    Cytoscape,
  },

  props: {
    pipeline: {
      type: Object,
    },
  },
});
</script>

<style scoped>
.app-data-pipeline {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
}

.app-data-pipeline__viz {
  flex: 1;
}
</style>
