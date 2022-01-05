<template>
  <svg>
    <defs>
      <marker
        id="arrowhead"
        markerWidth="12"
        markerHeight="10"
        refX="0"
        refY="5"
        orient="auto"
        markerUnits="userSpaceOnUse"
      >
        <path
          fill-rule="evenodd"
          clip-rule="evenodd"
          d="M11.429 4.4461C11.9213 4.65123 11.9213 5.34866 11.429 5.55379L0.876595 9.9507C0.392083 10.1526 -0.0997904 9.67894 0.0836632 9.18715L1.64561 5.00001L0.0836483 0.812732C-0.0998007 0.320946 0.392071 -0.152697 0.876581 0.0491848L11.429 4.4461Z"
          fill="#919CB2"
        />
      </marker>
    </defs>
    <path
      :d="dQuadratic"
      fill="none"
      stroke="#919CB2"
      stroke-width="2"
      stroke-linejoin="round"
      stroke-linecap="round"
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
    // Avoid that arrow gets hidden behind the target node
    endBeforeArrow(): { x: number; y: number } {
      return {
        x: this.end.x - 12, // width of the arrow
        y: this.end.y,
      };
    },

    dStraight(): string {
      return `M ${this.start.x} ${this.start.y} L ${this.endBeforeArrow.x} ${this.endBeforeArrow.y}`;
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
      L ${this.center.x} ${this.endBeforeArrow.y}
      L ${this.endBeforeArrow.x} ${this.endBeforeArrow.y}`;
    },

    // Quadratic curve rounding the taxi line
    dQuadratic(): string {
      return `M ${this.start.x} ${this.start.y}
      Q ${this.center.x} ${this.start.y}, ${this.center.x} ${this.center.y}
      Q ${this.center.x} ${this.endBeforeArrow.y}, ${this.endBeforeArrow.x} ${this.endBeforeArrow.y}`;
    },
  },
});
</script>

<style scoped></style>
