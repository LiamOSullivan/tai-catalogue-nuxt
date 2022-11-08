<template>
  <div class="col-lg-12 col-sm-12">
    <button
      type="button"
      class="btn btn-outline-primary m-1"
      style="width: 33%"
      @click.prevent="goToPrev()"
    >
      <font-awesome-icon icon="fa-solid fa-arrow-left" /> Back to results
    </button>
    <div id="metadata-content" class="row"></div>
  </div>
</template>
<script>
import Vue from "vue";
import "bootstrap/dist/css/bootstrap.css";
import { library } from "@fortawesome/fontawesome-svg-core";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { faArrowLeft } from "@fortawesome/free-solid-svg-icons";
library.add(faArrowLeft);
Vue.component("font-awesome-icon", FontAwesomeIcon);
Vue.config.productionTip = false;

export default Vue.extend({
  name: "Dataset",
  methods: {
    goToPrev() {
      this.$router.go(-1);
      // window.location.href = "/";
    },
  },
});
</script>
