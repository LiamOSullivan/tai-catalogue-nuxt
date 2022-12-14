<template>
  <component :is="tag">
    <template>
      <label for="count">Filtered Records count: </label>
      <small>{{ dsIndexes.length }}</small>
    </template>
  </component>
</template>

<script>
export default {
  inject: ["rdsIndexes"],
  props: {
    tag: {
      type: String,
      default: "div",
    },
  },
  computed: {
    /* Setup reactive injects */
    dsIndexes() {
      return this.rdsIndexes();
    },
  },
};
</script>
