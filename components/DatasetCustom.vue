<template>
  <component :is="tag">
    <template>
      {{ dsIndexes.length }}
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
