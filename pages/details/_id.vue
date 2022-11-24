<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-lg-3 col-sm-12">
        <button
          type="button"
          class="btn btn-outline-primary m-1"
          style="width: 100%"
          @click.prevent="goToPrev()"
        >
          <font-awesome-icon icon="fa-solid fa-arrow-left" /> Back
        </button>
      </div>
    </div>

    <div id="grid" v-if="res_title" class="container">
      <div class="res_title text-left m-3">
        <h2>{{ res_title }}</h2>
      </div>
      <div id="bbox-map__container" class="bbox-map card m-1">
        <div class="card-body" style="min-height: 30vh; height: 30vh">
          <MapBBox v-if="poly" :poly="poly" />
        </div>
      </div>
      <div v-if="date_range" class="card date_range text-left m-1">
        <div class="card-body">
          <h6 class="card-title">Date range</h6>
          <p class="card-text small">
            <small>{{ date_range }} </small>
          </p>
        </div>
      </div>

      <div
        v-for="(value, key) in data"
        :key="key"
        class="card text-left m-1"
        :class="key"
      >
        <div class="card-body">
          <h6 class="card-title">{{ mapName(key) }}</h6>
          <p class="card-text small">
            <small>{{
              value !== null ? capitalise(value) : "Not available"
            }}</small>
          </p>
        </div>
      </div>
      <div class="card text-left m-1 attributes">
        <div class="card-body">
          <h6 class="card-title">Attributes</h6>
          <div v-if="hasAttributes" class="card-text small">
            <b>Value type | Name | Unit</b>
            <ul>
              <li v-for="(attr, key) in attributes" :key="key">
                <small>
                  {{ attr["Value type"] }} | {{ attr.Name }} | {{ attr.Unit }}
                </small>
              </li>
            </ul>
          </div>
          <div v-else class="card-text small">
            <small> There are no attributes listed for this dataset </small>
          </div>
        </div>
      </div>
    </div>
    <div id="error-nodata" v-else class="container">
      <div class="text-left m-3">
        <h2>Error</h2>
        <p>{{ error_msg }}</p>
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
import validSites from "~/data/in-situ_valid_sites_attributes.json";

import Papa from "papaparse";

import { capitalise } from "../../utilities";
library.add(faArrowLeft);
Vue.component("font-awesome-icon", FontAwesomeIcon);
Vue.config.productionTip = false;

function getDateRangeString(start: string, end: string): string {
  const dateRangeString = `${start} to ${end}`;
  return dateRangeString || "Not available";
}

export default Vue.extend({
  name: "DatasetDetails",
  data: () => {
    const data: {
      data: any;
      poly: any;
      res_title: string;
      date_range: string;
      error_msg: string;
      attributes: string[];
      hasAttributes: boolean;
    } = {
      data: {},
      poly: null,
      res_title: null,
      date_range: null,
      error_msg: null,
      attributes: [],
      hasAttributes: false,
    };

    return data;
  },

  async created() {
    const baseURLJson =
      "https://tai-api.terrainai.com/api/v1/dc/dc-id-data/?format=json";
    const id = this.$route.params.id;
    console.log("getting details for row " + id);

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
      // console.log(displayProps);
      this.data = displayProps;
      this.poly =
        features[0].getGeometry() !== undefined
          ? features[0].getGeometry()
          : null;
      this.res_title = capitalise(res_title);
      this.date_range = getDateRangeString(date_from, date_to);

      this.hasAttributes = !!validSites[tai_id];
      if (this.hasAttributes) {
        const attrURL =
          "https://taidashboardlayers.blob.core.windows.net/dashboard-storage/catalogue_temp_data_sources/IE_CRA_attribute_table.csv";
        // let attributes: any = [];
        const self = this;
        Papa.parse(attrURL, {
          download: true,
          header: true,
          skipEmptyLines: true,
          complete: function (results: any) {
            if (results && results.errors) {
              if (results.errors.length > 0) {
                console.log("Results errors: ", results.errors);
              }
              if (results.data && results.data.length > 0) {
                console.log("Results:", results.data);
                self.attributes = results.data;
              }
            }
          },
        });
      }
    } catch (error) {
      console.warn("Error fetching data from Catalogue API", error);
      this.error_msg = `Error fetching data for id of ${this.$route.params.id} - are details correct?`;
    }
  },

  mounted() {},
  methods: {
    mapName(shortName: string) {
      const obj: any = nameMap;
      return obj[shortName] ? obj[shortName].long_name : shortName;
    },
    goToPrev() {
      // this.$router.go(-1);
      // console.log("history ", window.history.length);

      if (window.history.length > 1) {
        window.history.back();
      } else {
        window.location.href = "/";
      }
    },
    capitalise(s: string) {
      return (s && s[0].toUpperCase() + s.slice(1)) || "";
    },
  },
});
</script>

<style scoped>
#grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr;
  grid-template-rows: auto;
  gap: 0px 0px;
  grid-auto-flow: row;
  grid-template-areas:
    "res_title res_title res_title res_title res_title res_title"
    "res_abs res_abs res_abs ref bbox-map bbox-map"
    "topic_cat topic_cat sub_cat sub_cat bbox-map bbox-map"
    "res_type res_type res_lang res_lang bbox-map bbox-map"
    "keyword keyword date_range date_range name  spatial_res"
    "instrument lineage lineage attributes attributes attributes"
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

.attributes {
  grid-area: attributes;
}

#bbox-map__container {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  height: auto;
  padding: 0;
}
.card {
  height: auto;
  min-height: none;
}
.card-body {
  padding: 8px;
}
.card-text {
  overflow: auto;
  max-height: 240px;
}

h2,
h6 {
  font-weight: 400;
}

p,
ul,
li,
small {
  font-weight: 200;
}

b {
  font-weight: 300;
}

ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>




