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
    <line
      :x1="x1"
      :y1="y1"
      :x2="x2"
      :y2="y2"
      stroke="#000"
      stroke-width="1"
      marker-end="url(#arrowhead)"
    />
  </svg>
</template>

<script>
export default {
  name: "Edge",

  props: ["x1", "x2", "y1", "y2"],
};
</script>

<style scoped></style>
