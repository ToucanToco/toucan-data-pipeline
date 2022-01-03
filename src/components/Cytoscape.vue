<template>
  <div class="cytoscape-viz"></div>
</template>

<script lang="ts">
import Vue from "vue";
import cytoscape from "cytoscape";

export default Vue.extend({
  name: "Cytoscape",

  data() {
    // eslint-disable-next-line
    return {} as any as { cyInstance: cytoscape.Core };
  },

  created() {
    this.cyInstance = cytoscape();
  },

  mounted() {
    this.cyInstance.mount(this.$el);
  },

  destroyed() {
    this.cyInstance.destroy();
  },
});
</script>

<style scoped></style>
