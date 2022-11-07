<template>
  <div @click="goToPrev()">Back</div>
</template>
<script>
import Vue from "vue";
export default Vue.extend({
  name: "Dataset",
  methods: {
    goToPrev() {
      // ...
      // Do other logic like logging, etc.
      // ...

      // Tell router to go back one
      this.$router.go(-1);
    },
  },
});
</script>
