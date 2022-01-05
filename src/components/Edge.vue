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
    <path
      :d="dQuadratic"
      fill="none"
      stroke="#000"
      stroke-width="1"
      marker-end="url(#arrowhead)"
    ></path>
  </svg>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";

export default Vue.extend({
  name: "Edge",

  props: {
    start: {
      type: Object as PropType<{ x: number; y: number }>,
    },
    end: {
      type: Object as PropType<{ x: number; y: number }>,
    },
  },

  computed: {
    dStraight(): string {
      return `M ${this.start.x} ${this.start.y} L ${this.end.x} ${this.end.y}`;
    },

    center(): { x: number; y: number } {
      return {
        x: (this.end.x + this.start.x) / 2,
        y: (this.end.y + this.start.y) / 2,
      };
    },

    // 3-part line
    dTaxi(): string {
      return `M ${this.start.x} ${this.start.y}
      L ${this.center.x} ${this.start.y}
      L ${this.center.x} ${this.end.y}
      L ${this.end.x} ${this.end.y}`;
    },

    // Quadratic curve rounding the taxi line
    dQuadratic(): string {
      return `M ${this.start.x} ${this.start.y}
      Q ${this.center.x} ${this.start.y}, ${this.center.x} ${this.center.y}
      Q ${this.center.x} ${this.end.y}, ${this.end.x} ${this.end.y}`;
    },
  },
});
</script>

<style scoped></style>
