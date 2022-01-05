<template>
  <div class="cytoscape-viz"></div>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";
import cytoscape from "cytoscape";
import dagre from "cytoscape-dagre";

cytoscape.use(dagre);

export default Vue.extend({
  name: "Cytoscape",

  props: {
    graph: {
      type: Object as PropType<{
        nodes: { type: string; name: string; id: string }[];
        edges: { from: string; to: string }[];
      }>,
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
            "taxi-direction": "rightward",
            "taxi-turn-min-distance": "25px",
          },
        },
      ],
    });

    this.updateGraph();
  },

  destroyed() {
    this.cyInstance.destroy();
  },

  methods: {
    updateGraph() {
      this.cyInstance.remove("*");
      this.cyInstance.add({
        nodes: this.graph.nodes.map((d): cytoscape.NodeDefinition => {
          return {
            data: Object.freeze({ ...d }),
          };
        }),
        edges: this.graph.edges.map(
          (d): cytoscape.EdgeDefinition => ({
            data: {
              source: Object.freeze(d.from),
              target: Object.freeze(d.to),
            },
          })
        ),
      });

      this.cyInstance
        .layout({
          name: "dagre",
          animate: false,
          rankDir: "LR",
        })
        .run();
    },
  },
});
</script>

<style scoped>
.cytoscape-viz {
  text-align: left;
}
</style>
