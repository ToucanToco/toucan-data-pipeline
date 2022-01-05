<template>
  <div
    class="graph"
    :style="{
      height: height + 'px',
      width: width + 'px',
      flex: width && height ? 'none' : undefined,
    }"
  >
    <Edge
      class="edge"
      :key="e.id"
      :start="e.start"
      :end="e.end"
      v-for="e in edges"
      :style="{
        opacity: hasHighlighedNode ? (e.highlighted ? 1 : 0.3) : 1,
      }"
    ></Edge>
    <Node
      class="node"
      :x="n.x"
      :y="n.y"
      :label="n.label"
      :key="n.id"
      v-for="n in nodes"
      :style="{
        top: n.y - 60 / 2 + 'px',
        left: n.x - 150 / 2 + 'px',
        opacity: hasHighlighedNode ? (n.highlighted ? 1 : 0.5) : 1,
      }"
      @mouseenter.native="highlightNode(n.id)"
      @mouseleave.native="resetHighlight()"
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
    nodes: {
      x: number;
      y: number;
      id: string;
      label: string;
      highlighted: boolean;
    }[];
    edges: {
      start: { x: number; y: number };
      end: { x: number; y: number };
      id: string;
      highlighted: boolean;
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
      nodes: {
        x: number;
        y: number;
        id: string;
        label: string;
        highlighted: boolean;
      }[];
      edges: {
        start: { x: number; y: number };
        end: { x: number; y: number };
        id: string;
        highlighted: boolean;
      }[];
      height?: number;
      width?: number;
    };
  },

  computed: {
    hasHighlighedNode(): boolean {
      return this.nodes.some((n) => n.highlighted);
    },
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
        highlighted: false,
      };
    });

    this.edges = this.dagreGraph.edges().map((id) => {
      const e = this.dagreGraph.edge(id);
      const sourceNode = this.dagreGraph.node(id.v);
      const targetNode = this.dagreGraph.node(id.w);
      return {
        points: e.points,
        start: {
          // Right of the source node
          x: sourceNode.x + sourceNode.width / 2,
          y: sourceNode.y,
        },
        end: {
          // Left of the target node
          x: targetNode.x - targetNode.width / 2,
          y: targetNode.y,
        },
        id: id.v + "->" + id.w,
        highlighted: false,
      };
    });
  },

  methods: {
    resetHighlight() {
      console.log("resetHighlight");
      this.nodes.forEach((n) => (n.highlighted = false));
      this.edges.forEach((n) => (n.highlighted = false));
    },

    highlightNode(nodeId: string) {
      const connectingEdges = (
        this.dagreGraph.edges() as { v: string; w: string }[]
      ).filter((e) => e.v === nodeId || e.w === nodeId);
      const nodesToHighlight = Array.from(
        new Set(
          connectingEdges.flatMap((e: { v: string; w: string }) => [e.v, e.w])
        )
      );
      this.nodes.forEach(
        (n) => (n.highlighted = nodesToHighlight.includes(n.id))
      );

      const edgesToHighlight = connectingEdges.map((e) => e.v + "->" + e.w);
      this.edges.forEach(
        (e) => (e.highlighted = edgesToHighlight.includes(e.id))
      );
    },
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
  transition: opacity 100ms;
}

.edge {
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}
</style>
