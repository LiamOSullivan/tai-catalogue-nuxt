<template>
  <div class="row">
    <div class="col-lg-3 col-sm-12">
      <button
        type="button"
        class="btn btn-outline-primary m-1"
        style="width: 100%"
        @click.prevent="goToPrev()"
      >
        <font-awesome-icon icon="fa-solid fa-arrow-left" /> Back to results
      </button>
    </div>

    <div class="container-fluid">
      <div class="row">
        <div class="col-md-12">
          <div class="page-header">
            <h2>{{ res_title }}</h2>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md-8">
          <p>
            {{ abstract }}
          </p>
        </div>
        <div class="col-md-4" style="min-height: 40vh">
          <MapBBox v-if="poly" :poly="poly" />
        </div>
      </div>
      <div class="row">
        <div class="col-md-12" id="card-grid">
          <div v-for="(value, key) in data" class="card text-left m-2">
            <div class="card-body">
              <h6 class="card-title">{{ mapName(key) }}:</h6>
              <p class="card-text small">
                {{ value !== null ? value : "Unknown" }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import Vue from "vue";
import axios from "axios";
import "bootstrap/dist/css/bootstrap.css";
import { library } from "@fortawesome/fontawesome-svg-core";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { faArrowLeft } from "@fortawesome/free-solid-svg-icons";
import GeoJSON from "ol/format/GeoJSON";
import nameMap from "../../data/propsNameMap.json";
library.add(faArrowLeft);
Vue.component("font-awesome-icon", FontAwesomeIcon);
Vue.config.productionTip = false;

export default Vue.extend({
  name: "DatasetDetails",
  data: () => {
    const data: {
      data: any;
      abstract: string;
      ref_title: string;
      poly: any;
    } = { data: {}, poly: null, abstract: "", res_title: "" };

    return data;
  },

  async created() {
    const baseURLJson =
      "https://tai-api.terrainai.com/api/v1/dc/dc-id-data/?format=json";
    const id = this.$route.query.id;
    console.log("getting details for row " + id);
    try {
      const res = await axios.get(`${baseURLJson}&dc_id=${id}`);
      const features = new GeoJSON().readFeatures(res.data[0], {
        dataProjection: "EPSG:4326",
        featureProjection: "EPSG:3857",
      });

      for (const key in features[0].getProperties()) {
        if (
          Object.prototype.hasOwnProperty.call(features[0].getProperties(), key)
        ) {
          const element = features[0].getProperties()[key];
          console.log(key, element);
        }
      }
      // destructure to rm unwanted props from display
      const {
        geometry,
        res_title,
        data_viewer,
        res_abs,
        res_loc,
        uri,
        dc_id,
        tai_id,
        ...displayProps
      } = features[0].getProperties();
      this.data = displayProps;
      this.poly =
        features[0].getGeometry() !== undefined
          ? features[0].getGeometry()
          : null;
      this.abstract = res_abs;
      this.res_title = res_title.trim();

      console.log(this.data);
    } catch (error) {
      console.warn("Error fetching data from Catalogue API", error);
    }
  },

  mounted() {},
  methods: {
    mapName(shortName: string) {
      return nameMap[shortName] ? nameMap[shortName].long_name : shortName;
    },
    goToPrev() {
      this.$router.go(-1);
      // window.location.href = "/";
    },
  },
});
</script>

<style scoped>
#card-grid {
  display: flex;
  flex-wrap: wrap;
}
</style>

