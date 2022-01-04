<template>
  <div class="cytoscape-viz"></div>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";
import cytoscape from "cytoscape";
import elk from "cytoscape-elk";
import dagre from "cytoscape-dagre";

import PAGINATION_GRAPH from "@/components/app-pagination.json";

cytoscape.use(elk);
cytoscape.use(dagre);

const NODES: cytoscape.NodeDefinition[] = PAGINATION_GRAPH.nodes.map(
  (d): cytoscape.NodeDefinition => ({
    data: d,
  })
);
const EDGES: cytoscape.EdgeDefinition[] = PAGINATION_GRAPH.edges.map(
  (d): cytoscape.EdgeDefinition => ({
    data: {
      source: d.from,
      target: d.to,
    },
  })
);

const LAYOUT_OPTIONS = {
  breadthfirst: {
    name: "breadthfirst",
    directed: true,
  },
  elk: {
    name: "elk",
    elk: {
      algorithm: "layered",
      "elk.direction": "DOWN",
    },
  },
  dagre: {
    name: "dagre",
  },
};

export default Vue.extend({
  name: "Cytoscape",

  props: {
    layout: {
      type: String as PropType<"breadthfirst" | "elk" | "dagre">,
      default: () => "breadthfirst",
    },
  },

  data() {
    // eslint-disable-next-line
    return {} as any as { cyInstance: cytoscape.Core };
  },

  mounted() {
    this.cyInstance = cytoscape({
      // headless: true,
      container: this.$el as HTMLElement,

      elements: {
        nodes: NODES,
        edges: EDGES,
      },

      style: [
        // the stylesheet for the graph
        {
          selector: "node",
          style: {
            "background-color": "#666",
            label: "data(id)",
          },
        },

        {
          selector: "edge",
          style: {
            width: 3,
            "line-color": "#ccc",
            "target-arrow-color": "#ccc",
            "target-arrow-shape": "triangle",
            "curve-style": "bezier",
          },
        },
      ],

      // @ts-ignore
      layout: LAYOUT_OPTIONS[this.layout],
    });
  },

  destroyed() {
    this.cyInstance.destroy();
  },
});
</script>

<style scoped>
.cytoscape-viz {
  text-align: left;
}
</style>
