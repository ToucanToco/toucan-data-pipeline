<template>
  <div class="node" :class="`node--${type}`" :alt="label">
    <div class="label">{{ label }}</div>
  </div>
</template>

<script>
export default {
  name: "Node",

  props: ["label", "x", "y", "type"],
};
</script>

<style scoped>
.node {
  width: 30px;
  height: 30px;
  border-radius: 5px;
  position: relative;
  box-shadow: 0px 0px 12px 0px #0000000a;
}

.node.node--datasource {
  width: 60px;
  height: 60px;
  background-color: #e7ebf3;
  border: 1px solid #d4dae6;
}
.node.node--provider {
  width: 60px;
  height: 60px;
}
.node.node--domain {
  width: 150px;
  height: 60px;
  background-color: white;
}
.node.node--story {
  width: 60px;
  height: 60px;
}
.node.node--unknown {
  background-color: gray;
}

.label {
  position: absolute;
  text-align: center;
  top: calc(100%);
  padding-top: 0.3em;
  height: 2em;
  left: -10px;
  right: -10px;
  text-overflow: ellipsis;
  overflow: hidden;
}
</style>
