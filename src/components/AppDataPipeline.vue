<template>
  <div class="app-data-pipeline">
    <h1>App data pipeline</h1>
    <!--    <Cytoscape-->
    <!--      :graph="BRAND_PERF_GRAPH"-->
    <!--      class="app-data-pipeline__viz"-->
    <!--    ></Cytoscape>-->
    <Dagre :graph="BI_ACHATS_GRAPH"></Dagre>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
// import Cytoscape from "@/components/Cytoscape.vue";
import Dagre from "@/components/Dagre.vue";

import PAGINATION_GRAPH from "@/components/app-pagination.json";
import BRAND_PERF_GRAPH from "@/components/app-brand-perf.json";
import DRIVE_REPORTING_GRAPH from "@/components/app-drive-reporting.json";
import BP_HP_GRAPH from "@/components/app-bp-hp.json";
import BI_ACHATS_GRAPH from "@/components/app-bi-achats.json";

export default Vue.extend({
  name: "AppDataPipeline",

  components: {
    // Cytoscape,
    Dagre,
  },

  props: {
    pipeline: {
      type: Object,
    },
  },

  data() {
    return {
      PAGINATION_GRAPH,
      BRAND_PERF_GRAPH,
      DRIVE_REPORTING_GRAPH,
      BP_HP_GRAPH,
      BI_ACHATS_GRAPH,
    };
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
