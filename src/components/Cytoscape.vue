<template>
  <div class="cytoscape-viz"></div>
</template>

<script lang="ts">
import Vue from "vue";
import cytoscape from "cytoscape";

export default Vue.extend({
  name: "Cytoscape",

  mounted() {
    var cy = cytoscape({
      container: this.$el,
    });
  },
});
</script>

<style scoped></style>
