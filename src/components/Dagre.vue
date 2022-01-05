<template>
  <div
    class="graph"
    :style="{
      height: height + 'px',
      width: width + 'px',
      flex: width && height ? 'none' : undefined,
    }"
  >
    <Edge class="edge" :key="e.id" :points="e.points" v-for="e in edges"></Edge>
    <Node
      class="node"
      :x="n.x"
      :y="n.y"
      :label="n.label"
      :key="n.id"
      v-for="n in nodes"
      :style="{ top: n.y - 60 / 2 + 'px', left: n.x - 150 / 2 + 'px' }"
    ></Node>
  </div>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";
import dagre from "dagre";

import Node from "./Node.vue";
import Edge from "./Edge.vue";

export default Vue.extend({
  name: "Dagre",

  components: {
    Node,
    Edge,
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
    nodes: { x: number; y: number; id: string; label: string }[];
    edges: {
      points: { x: number; y: number }[];
      id: string;
    }[];
    height?: number;
    width?: number;
  } {
    return {
      nodes: [],
      edges: [],
      height: undefined,
      width: undefined,
      // eslint-disable-next-line @typescript-eslint/no-explicit-any
    } as any as {
      dagreGraph: dagre.graphlib.Graph;
      nodes: { x: number; y: number; id: string; label: string }[];
      edges: {
        points: { x: number; y: number }[];
        id: string;
      }[];
      height?: number;
      width?: number;
    };
  },

  created() {
    this.dagreGraph = new dagre.graphlib.Graph();
    this.dagreGraph.setGraph({
      rankdir: "LR",
      align: "UL",
      marginx: 20,
      ranksep: 200,
      // ranker: "tight-tree",
    });

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

    this.height = this.dagreGraph.graph().height;
    this.width = this.dagreGraph.graph().width;

    this.nodes = this.dagreGraph.nodes().map((id) => {
      const n = this.dagreGraph.node(id);
      return {
        x: n.x,
        y: n.y,
        id: id,
        label: n.label as string,
      };
    });

    this.edges = this.dagreGraph.edges().map((id) => {
      const e = this.dagreGraph.edge(id);
      return {
        points: e.points,
        id: id.v + "->" + id.w,
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

.node,
.edge {
  position: absolute;
}

.edge {
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}
</style>
