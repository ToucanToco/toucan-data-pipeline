<template>
  <div>App data pipeline</div>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
  name: 'AppDataPipeline',

  props: {
    pipeline: {
      type: Object
    }
  }
});
</script>

<style scoped></style>
