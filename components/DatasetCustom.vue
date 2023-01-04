<template>
  <div>
    <label for="count">Filtered Records count: </label>
    <small>{{ dsIndexes.length }}</small>
  </div>
</template>

<script>
export default {
  inject: ["rdsIndexes"],
  props: {
    // tag: {
    //   type: String,
    //   default: "div",
    // },
  },
  computed: {
    /* Setup reactive injects */
    dsIndexes() {
      return this.rdsIndexes();
    },
  },
};
</script>
