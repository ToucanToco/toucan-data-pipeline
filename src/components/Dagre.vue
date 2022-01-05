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
      :type="n.type"
      :key="n.id"
      v-for="n in nodes"
      :style="{
        top: n.y - (NODE_TYPES[n.type] || NODE_TYPES.unknown).height / 2 + 'px',
        left: n.x - (NODE_TYPES[n.type] || NODE_TYPES.unknown).width / 2 + 'px',
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
import NODE_TYPES from "./node-types";

export default Vue.extend({
  name: "Dagre",

  components: {
    Node,
    Edge,
  },

  props: {
    graph: {
      type: Object as PropType<{
        nodes: {
          type: "datasource" | "provider" | "domain" | "story";
          name: string;
          id: string;
        }[];
        edges: { from: string; to: string }[];
      }>,
    },
  },

  data(): {
    dagreGraph: dagre.graphlib.Graph;
    NODE_TYPES: typeof NODE_TYPES;
    nodes: {
      x: number;
      y: number;
      id: string;
      label: string;
      type: "datasource" | "provider" | "domain" | "story" | "unknown";
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
      NODE_TYPES: NODE_TYPES,
      nodes: [],
      edges: [],
      height: undefined,
      width: undefined,
      // eslint-disable-next-line @typescript-eslint/no-explicit-any
    } as any as {
      dagreGraph: dagre.graphlib.Graph;
      NODE_TYPES: typeof NODE_TYPES;

      nodes: {
        x: number;
        y: number;
        id: string;
        label: string;
        type: "datasource" | "provider" | "domain" | "story" | "unknown";
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
      ranksep: 60,
      // ranker: "tight-tree",
    });

    this.dagreGraph.setDefaultEdgeLabel(function () {
      return {};
    });

    this.graph.nodes.forEach((n) => {
      const nodeType = NODE_TYPES[n.type] || NODE_TYPES.unknown;
      this.dagreGraph.setNode(n.id, {
        label: n.name,
        width: nodeType.width,
        height: nodeType.height,
      });
    });

    this.graph.edges
      // Reject edges that connect inexisting nodes
      .filter(
        (e) =>
          this.graph.nodes.find((n) => n.id === e.from) &&
          this.graph.nodes.find((n) => n.id === e.to)
      )
      .forEach((e) => {
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
      const n = this.dagreGraph.node(id); // In layout graph
      const d = this.graph.nodes.find((d) => d.id == id); // In provided data
      console.log(id);
      return {
        x: n.x,
        y: n.y,
        id: id,
        label: n.label as string,
        type: d ? d.type : "unknown",
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
  background-color: #f5f6fa;
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
