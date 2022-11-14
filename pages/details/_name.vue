<template>
  <div class="container-fluid">
    <div class="res_title text-left m-3">
      <h2>{{ res_title }}</h2>
    </div>
    <div id="bbox-map__container" class="bbox-map card text-left m-2">
      <div class="card-body" style="min-height: 40vh">
        <MapBBox v-if="poly" :poly="poly" />
      </div>
    </div>
    <div class="card date_range text-left m-2">
      <div class="card-body">
        <h6 class="card-title">Date range</h6>
        <p class="card-text small"></p>
      </div>
    </div>

    <div
      v-for="(value, key) in data"
      :key="key"
      class="card text-left m-2"
      :class="key"
    >
      <div class="card-body">
        <h6 class="card-title">{{ mapName(key) }}</h6>
        <p class="card-text small">
          {{ value !== null ? value : "Unknown" }}
        </p>
      </div>
    </div>

    <!-- <div class="res_title"></div>
    <div class="res_abs"></div>
    <div class="bbox-map"></div>
    <div class="ref"></div>
    <div class="topic_cat"></div>
    <div class="sub_cat"></div>
    <div class="keyword"></div>
    <div class="res_type">
      <div class="res_lang"></div>
    </div>
    <div class="spatial_res"></div>
    <div class="name"></div>
    <div class="date_range"></div>
    <div class="lineage"></div>
    <div class="meta_date"></div>
    <div class="meta_lang"></div>
    <div class="instrument"></div>
    <div class="resp_org"></div>
    <div class="poc"></div>
    <div class="conditions"></div>
    <div class="limitations"></div>
    <div class="links">
      <div class="res_loc"></div>
      <div class="uri"></div>
    </div>
    <div class="viewers">
      <div class="data_viewer"></div>
      <div class="dashboard_view"></div>
    </div> -->

    <!-- <div class="row">
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
    </div> -->
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
      poly: any;
      res_title: string;
    } = {
      data: {},
      poly: null,
      res_title: "Resource Title",
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
        res_loc,
        attr,
        conformity,
        status,
        data_viewer,
        uri,
        dc_id,
        tai_id,
        date_from,
        date_to,
        ...displayProps
      } = features[0].getProperties();
      console.log(displayProps);
      this.data = displayProps;
      this.poly =
        features[0].getGeometry() !== undefined
          ? features[0].getGeometry()
          : null;
      this.res_title = res_title;
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
.container-fluid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
  gap: 0px 0px;
  grid-auto-flow: row;
  grid-template-areas:
    "res_title res_title res_title res_title res_title res_title"
    "res_abs res_abs res_abs ref bbox-map bbox-map"
    "topic_cat topic_cat sub_cat sub_cat bbox-map bbox-map"
    "res_type res_type res_lang res_lang bbox-map bbox-map"
    "keyword keyword date_range date_range name name"
    "instrument lineage lineage lineage spatial_res spatial_res"
    "resp_org resp_org poc poc meta_date meta_date"
    "resp_org resp_org poc poc meta_lang meta_lang"
    "conditions conditions conditions limitations limitations limitations"
    ". . . . . .";
}

.res_title {
  grid-area: res_title;
}

.res_abs {
  grid-area: res_abs;
}

.bbox-map {
  grid-area: bbox-map;
}

.ref {
  grid-area: ref;
}

.topic_cat {
  grid-area: topic_cat;
}

.sub_cat {
  grid-area: sub_cat;
}

.keyword {
  grid-area: keyword;
}

.spatial_res {
  grid-area: spatial_res;
}

.name {
  grid-area: name;
}

.date_range {
  grid-area: date_range;
}

.lineage {
  grid-area: lineage;
}

.meta_date {
  grid-area: meta_date;
}

.meta_lang {
  grid-area: meta_lang;
}

.instrument {
  grid-area: instrument;
}

.resp_org {
  grid-area: resp_org;
}

.poc {
  grid-area: poc;
}

.conditions {
  grid-area: conditions;
}

.limitations {
  grid-area: limitations;
}

.res_type {
  grid-area: res_type;
}

.res_lang {
  grid-area: res_lang;
}

#bbox-map__container {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}
</style>

