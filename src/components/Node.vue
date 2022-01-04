<template>
  <div class="node">
    <div class="label">{{ label }}</div>
  </div>
</template>

<script>
export default {
  name: "Node",

  props: ["label", "x", "y"],
};
</script>

<style scoped>
.node {
  width: 150px;
  height: 60px;
  border-radius: 5px;
  background-color: #2c3e50;
  position: relative;
}

.label {
  position: absolute;
  text-align: center;
  bottom: -1.3em;
  left: 0;
  right: 0;
}
</style>
