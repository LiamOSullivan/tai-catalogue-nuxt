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
          <div class="page-header text-left m-2">
            <h2>{{ res_title }}</h2>
          </div>
        </div>
      </div>
      <div class="row card-grid">
        <div class="col-md-8">
          <div class="card text-left m-2">
            <div class="card-body">
              <h6 class="card-title">Resource abstract</h6>
              <p class="card-text small">
                {{ abstract }}
              </p>
            </div>
          </div>
        </div>
        <div class="col-md-8 card-grid">
          <div class="col-md-4 card text-left m-2">
            <div class="card-body">
              <h6 class="card-title">Reference</h6>
              <p class="card-text small">
                {{ ref }}
              </p>
            </div>
          </div>
          <div class="col-md-4 card text-left m-2">
            <div class="card-body">
              <h6 class="card-title">Topic category</h6>
              <p class="card-text small">
                {{ topic_cat }}
              </p>
            </div>
          </div>
          <div class="card col-md-4 text-left m-2">
            <div class="card-body">
              <h6 class="card-title">Sub-category</h6>
              <p class="card-text small">
                {{ sub_cat }}
              </p>
            </div>
          </div>
        </div>
        <div id="bbox-map__container" class="col-md-4" style="min-height: 40vh">
          <MapBBox v-if="poly" :poly="poly" />
        </div>
      </div>
      <div class="row">
        <div class="col-md-12 card-grid">
          <div
            v-for="(value, key) in data"
            :key="key"
            class="card text-left m-2"
          >
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
      res_title: string;
      ref: string;
      topic_cat: string;
      sub_cat: string;
      poly: any;
    } = {
      data: {},
      poly: null,
      abstract: "",
      res_title: "",
      ref: "",
      topic_cat: "",
      sub_cat: "",
    };

    return data;
  },

  async created() {
    const baseURLJson =
      "https://tai-api.terrainai.com/api/v1/dc/dc-id-data/?format=json";
    const id = this.$route.query.id;
    // console.log("getting details for row " + id);
    try {
      const res = await axios.get(`${baseURLJson}&dc_id=${id}`);
      const features = new GeoJSON().readFeatures(res.data[0], {
        dataProjection: "EPSG:4326",
        featureProjection: "EPSG:3857",
      });

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
        date_from,
        date_to,
        topic_cat,
        sub_cat,
        ref,
        ...displayProps
      } = features[0].getProperties();
      console.log(displayProps);
      this.data = displayProps;
      this.poly =
        features[0].getGeometry() !== undefined
          ? features[0].getGeometry()
          : null;
      this.abstract = res_abs.trim();
      this.res_title = res_title.trim();
      this.ref = ref.trim();
      this.topic_cat = topic_cat.trim();
      this.sub_cat = sub_cat.trim();
    } catch (error) {
      console.warn("Error fetching data from Catalogue API", error);
    }
  },

  mounted() {},
  methods: {
    mapName(shortName: string) {
      const obj: any = nameMap;
      return obj[shortName] ? obj[shortName].long_name : shortName;
    },
    goToPrev() {
      this.$router.go(-1);
      // window.location.href = "/";
    },
  },
});
</script>

<style scoped>
* {
  box-sizing: border-box;
}

.card-grid {
  display: flex;
  flex-wrap: wrap;
  box-sizing: border-box;
}
#bbox-map__container {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}
</style>

