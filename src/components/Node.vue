<template>
  <div class="node" :class="`node--${type}`" :alt="label">
    <div class="icon" aria-hidden="true"></div>
    <div class="label" :class="`label--${labelPosition}`">
      {{ label }}
    </div>
  </div>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";

export default Vue.extend({
  name: "Node",

  props: {
    label: {
      type: String as PropType<string>,
    },
    type: {
      type: String as PropType<string>,
    },
    x: {
      type: Number as PropType<number>,
    },
    y: {
      type: Number as PropType<number>,
    },
  },

  computed: {
    labelPosition(): "inside" | "below" {
      switch (this.type) {
        case "domain":
        case "story":
          return "inside";
        default:
          return "below";
      }
    },
  },
});
</script>

<style scoped>
.node {
  width: 30px;
  height: 30px;
  border-radius: 8px;
  position: relative;
  box-shadow: 0px 0px 12px 0px #0000000a;
  display: flex;
  align-items: center;
}

.node.node--unknown {
  background-color: gray;
}

.node.node--datasource {
  width: 72px;
  height: 72px;
  background-color: #e7ebf3;
  border: 1px solid #d4dae6;
}
.node.node--provider {
  width: 72px;
  height: 72px;
}
.node.node--domain {
  width: 160px;
  height: 48px;
  background-color: white;
}
.node.node--story {
  width: 91px;
  height: 48px;
  border-radius: 33px;
  border: 4px solid #edc002;
  background-color: #fffbeb;
}

.label {
  font-weight: 600;
  font-size: 10px;
  line-height: 11px;
  /* identical to box height, or 110% */
  letter-spacing: -0.4px;
}

.label--inside {
  flex: 1;
  text-overflow: ellipsis;
  overflow: hidden;
}

.label.label--below {
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
