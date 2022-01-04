<template>
  <div class="cytoscape-viz"></div>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";
import cytoscape from "cytoscape";
import cola from "cytoscape-cola";

import PAGINATION_GRAPH from "@/components/app-pagination.json";

cytoscape.use(cola);

const NODES: cytoscape.NodeDefinition[] = PAGINATION_GRAPH.nodes.map(
  (d, i): cytoscape.NodeDefinition => {
    return {
      data: d,
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

export default Vue.extend({
  name: "Cytoscape",

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

      // layout: LAYOUT_OPTIONS[this.layout],
    });

    this.cyInstance
      .layout({
        name: "cola",
        // @ts-ignore
        animate: false,

        // @ts-ignore
        flow: { axis: "x", minSeparation: 250 },
        nodeDimensionsIncludeLabels: true,

        // @ts-ignore
        alignment: {
          vertical: [
            // All providers and datasources should be aligned to the left
            this.cyInstance
              .nodes()
              .filter((n) =>
                ["provider", "datasource"].includes(n.data("type"))
              )
              .map((n) => ({ node: n, offset: 0 })),
          ],
        },
      })
      .run();
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
