<template>
  <div class="graph">
    <Node
      class="node"
      :x="n.x"
      :y="n.y"
      :label="n.label"
      :key="n.id"
      v-for="n in nodes"
      :style="{top: (n.y - 60/2) + 'px', left: (n.x - 150/2) + 'px'}"
    ></Node>
  </div>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";
import dagre from "dagre";

import Node from "./Node.vue";

export default Vue.extend({
  name: "Dagre",

  components: {
    Node,
  },

  props: {
    graph: {
      type: Object as PropType<{
        nodes: { type: string; name: string; id: string }[];
        edges: { from: string; to: string }[];
      }>,
    },
  },

  data(): {
    dagreGraph: dagre.graphlib.Graph;
    nodes: { x: number; y: number }[];
  } {
    // eslint-disable-next-line
    return {
      nodes: [],
    } as any as {
      dagreGraph: dagre.graphlib.Graph;
      nodes: { x: number; y: number }[];
    };
  },

  created() {
    this.dagreGraph = new dagre.graphlib.Graph();
    this.dagreGraph.setGraph({});

    this.dagreGraph.setDefaultEdgeLabel(function () {
      return {};
    });

    this.graph.nodes.forEach((n) => {
      this.dagreGraph.setNode(n.id, { label: n.name, width: 150, height: 60 });
    });

    this.graph.edges.forEach((e) => {
      this.dagreGraph.setEdge(e.from, e.to);
    });

    dagre.layout(this.dagreGraph);
  },

  mounted() {
    this.dagreGraph.graph().width = this.$el.clientWidth;
    this.dagreGraph.graph().height = this.$el.clientHeight;

    dagre.layout(this.dagreGraph);

    this.nodes = this.dagreGraph
      .nodes()
      .map((id) => {
        const n = this.dagreGraph.node(id);
        return {
          x: n.x,
          y: n.y,
          id: id,
          label: n.label,
        };
      });
  },
});
</script>

<style scoped>
.graph {
  width: 100%;
  height: 100%;
  position: relative;
}

.node {
  position: absolute;
}
</style>
