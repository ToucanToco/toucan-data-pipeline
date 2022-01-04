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
  (d, i): cytoscape.NodeDefinition => {
    return {
      data: d,
      position: ['provider', 'datasource'].includes(d.type) ? { x: 0, y: i * 100 } : undefined,
      locked: ['provider', 'datasource'].includes(d.type)
    };
  }
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
      "elk.direction": "RIGHT",
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
            width: "50px",
            shape: "round-rectangle",
            padding: "20px",
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
            "curve-style": "taxi",
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
