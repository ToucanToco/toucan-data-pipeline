<template>
  <svg>
    <defs>
      <marker
        id="arrowhead"
        markerWidth="10"
        markerHeight="7"
        refX="7"
        refY="3.5"
        orient="auto"
      >
        <polygon points="0 0, 10 3.5, 0 7" />
      </marker>
    </defs>
    <polyline
      :points="polylinePoints"
      fill="none"
      stroke="#000"
      stroke-width="1"
      marker-end="url(#arrowhead)"
    ></polyline>
  </svg>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";

export default Vue.extend({
  name: "Edge",

  props: {
    points: {
      type: Object as PropType<{ x: number; y: number }[]>,
    },
  },

  computed: {
    polylinePoints(): string {
      return this.points
        .map((p: { x: number; y: number }) => `${p.x},${p.y}`)
        .join(" ");
    },
  },
});
</script>

<style scoped></style>
