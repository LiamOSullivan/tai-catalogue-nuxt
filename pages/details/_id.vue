<template>
  <div class="container-fluid">
    <header>
      <div class="row no-gutters" id="top-bar">
        <div class="col">
          <h4 class="page-head">
            <a href="/">Terrain-AI</a>
            <span> | Data Catalogue </span>
            <!-- <img
              style="height: 32px"
              src="/airbus_branding/logo.svg"
              alt="airbus company logo"
            /> -->
          </h4>
        </div>
        <div class="col page-head__title">
          <h6 id="page-title" class="page-head bold" style="text-align: right">
            Log In <span>&#9888;</span>
          </h6>
        </div>
      </div>
    </header>

    <div id="grid" v-if="res_title" class="container">
      <div class="res_title text-left m-3">
        <h3>{{ res_title }}</h3>
      </div>
      <div class="back">
        <button
          type="button"
          class="btn m-1 btn-outline-secondary"
          style="width: 100%"
          @click.prevent="goToPrev()"
        >
          Back to search
        </button>
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
            <b>Value type | Attribute | Unit</b>
            <ul>
              <li v-for="(attr, key) in attributes" :key="key">
                <small
                  v-if="
                    attr['Value type'] || attr['ICOS Value type'].length > 0
                  "
                >
                  {{ attr["Value type"] || attr["ICOS Value type"] }}
                  | {{ attr.Name || attr["ICOS attr"] }} |
                  {{ attr.Unit || attr["ICOS unit"] }}
                </small>
              </li>
            </ul>
          </div>
          <div v-else class="card-text small">
            <small> There are no attributes listed for this dataset </small>
          </div>
        </div>
      </div>
      <div class="card text-left m-1 supplemental">
        <div class="card-body">
          <h6 class="card-title">Supplemental Datasets</h6>
          <div v-if="hasSupplemental" class="card-text small">
            <b>URI | Resource Locator</b>
            <ul>
              <li v-for="(s, key) in supplemental" :key="key">
                <small> {{ s.uri + " | " + s.resource_locator }} </small>
              </li>
            </ul>
          </div>
          <div v-else class="card-text small">
            <small>
              There are no supplemental data listed for this dataset
            </small>
          </div>
        </div>
      </div>
      <div class="card text-left m-1 viewer">
        <div class="card-body">
          <div style="width: 60%">
            <h6 class="card-title">Data Viewer</h6>
            <div v-if="getViewerUrl()">
              <small>View example data for this dataset here</small>
            </div>
            <div v-else>
              <small>This dataset does not have an associated viewer </small>
            </div>
          </div>
          <div
            style="
              width: 30%;
              display: flex;
              justify-content: center;
              align-items: flex-start;
              padding-top: 24px;
            "
          >
            <button
              type="button"
              class="btn btn-sm btn-outline-secondary m-1"
              style="height: 60%"
              :disabled="!getViewerUrl()"
              @click="openLink(getViewerUrl())"
            >
              View Data
              <font-awesome-icon icon="fa-solid fa-up-right-from-square" />
            </button>
          </div>
        </div>
      </div>
    </div>
    <div id="error-nodata" v-else class="container">
      <div class="text-left m-3">
        <h2>Loading data...</h2>
        <p>{{ error_msg }}</p>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import Vue from "vue";
import axios from "axios";
import Papa from "papaparse";
import GeoJSON from "ol/format/GeoJSON";
import "bootstrap/dist/css/bootstrap.css";
import { library } from "@fortawesome/fontawesome-svg-core";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { faArrowLeft } from "@fortawesome/free-solid-svg-icons";
import { faUpRightFromSquare } from "@fortawesome/free-solid-svg-icons";
import { capitalise } from "../../utilities/search";
import nameMap from "../../data/propsNameMap.json";
import validSites from "~/data/in-situ_valid_sites_attributes.json";

library.add(faArrowLeft);
library.add(faUpRightFromSquare);
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
      tai_id: number;
      res_title: string;
      date_range: string;
      error_msg: string;
      attributes: string[];
      hasAttributes: boolean;
      supplemental: string[];
      hasSupplemental: boolean;
      data_viewer: string;
      project: string;
    } = {
      data: {},
      poly: null,
      tai_id: null,
      res_title: null,
      date_range: null,
      error_msg: null,
      attributes: [],
      hasAttributes: false,
      supplemental: [],
      hasSupplemental: false,
      data_viewer: null,
      project: null,
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
        attr,
        conformity,
        status,
        data_viewer,
        uri,
        dc_id,
        tai_id,
        date_from,
        date_to,
        project,
        ...displayProps
      } = features[0].getProperties();
      // console.log(displayProps);
      // console.log(tai_id);

      this.data = displayProps;
      this.poly =
        features[0].getGeometry() !== undefined
          ? features[0].getGeometry()
          : null;
      this.res_title = capitalise(res_title);
      this.date_range = getDateRangeString(date_from, date_to);
      this.tai_id = tai_id;
      console.log(displayProps);

      if (!!validSites[tai_id] && displayProps.ref === "in-situ") {
        const baseAttrURL =
          "https://taidashboardlayers.blob.core.windows.net/dashboard-storage/catalogue_temp_data_sources/";
        if (validSites[tai_id].attr_table) {
          this.hasAttributes = true;
          const attrURL = baseAttrURL + validSites[tai_id].attr_table;
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
                  // console.log("Results:", results.data);
                  self.attributes = results.data;
                }
              }
            },
          });
        }
      }

      if (tai_id == 14) {
        this.hasSupplemental = true;
        const self = this;
        Papa.parse(
          "https://taidashboardlayers.blob.core.windows.net/dashboard-storage/catalogue_temp_data_sources/IE_Cd2_UAV_uris.csv",
          {
            download: true,
            header: true,
            skipEmptyLines: true,
            complete: function (supp: any) {
              if (supp && supp.errors && supp.errors.length) {
                console.log("Supp errors: ", supp.errors);
              } else if (supp.data && supp.data.length > 0) {
                // console.log("supp.data: ", supp.data);
                self.supplemental = supp.data;
              }
            },
          }
        );
      }

      this.data_viewer = data_viewer;
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
    getViewerUrl() {
      // TODO: do empty table values return undefined?
      const hasUrl =
        this.data_viewer !== null &&
        this.data_viewer.toLowerCase() !== "none" &&
        this.data_viewer.toLowerCase() !== "tbd" &&
        this.data_viewer.toLowerCase() !== "tbc"
          ? this.data_viewer.toString()
          : false;
      return hasUrl;
    },

    getSupplemental() {
      if (this.tai_id !== 14) {
        return null;
      }
      return;
    },
    openLink(url: string) {
      window.open(url);
    },
  },
});
</script>

<style scoped>
h2,
h3,
h4,
h5,
h6 {
  font-weight: 400;
  color: #014356;
}

p,
ul,
li,
small {
  font-weight: 300;
  color: #014356;
}

a,
span {
  text-decoration: none;
  font-weight: 300;
  color: inherit;
}

b {
  font-weight: 400;
  color: #014356;
}

ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
.container-fluid {
  padding: 0;
}

.page-head {
  color: white;
}

#top-bar {
  background: #014356;
  color: white;
  padding: 14px 48px;
  /* height: 60px; */
  display: flex;
  align-items: center;
  min-width: 740px;
}
.row {
  display: -ms-flexbox;
  display: flex;
  -ms-flex-wrap: wrap;
  flex-wrap: wrap;
  margin-right: -15px;
  margin-left: -15px;
}
.no-gutters {
  margin-right: 0;
  margin-left: 0;
}

#grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr;
  grid-template-rows: auto;
  gap: 0px 0px;
  grid-auto-flow: row;
  grid-template-areas:
    "res_title res_title res_title res_title res_title back"
    "res_abs res_abs res_abs ref bbox-map bbox-map"
    "topic_cat topic_cat sub_cat sub_cat bbox-map bbox-map"
    "res_type res_type res_lang res_lang bbox-map bbox-map"
    "keyword keyword date_range date_range name  spatial_res"
    "instrument lineage lineage attributes attributes attributes"
    "viewer viewer viewer res_loc res_loc res_loc"
    "supplemental supplemental supplemental supplemental supplemental supplemental"
    "resp_org resp_org poc poc meta_date meta_date"
    "resp_org resp_org poc poc meta_lang meta_lang"
    "conditions conditions conditions limitations limitations limitations";
}

.res_title {
  grid-area: res_title;
}

.back {
  grid-area: back;
  color: #014356;
  display: flex;
  justify-content: flex-end;
  align-items: center;
}
.back button {
  color: #014356;
  outline-color: #014356;
}
.btn {
  max-height: 56px;
}

.btn:hover {
  color: white;
  background-color: #014356;
  border-color: #014356;
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

.res_loc {
  grid-area: res_loc;
}

.attributes {
  grid-area: attributes;
}

.viewer {
  grid-area: viewer;
}
.viewer .card-body {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.viewer button {
  color: #014356;
  outline-color: #014356;
  min-height: 48px;
}

.viewer button {
  color: #014356;
  outline-color: #014356;
}

.supplemental {
  grid-area: supplemental;
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
</style>




