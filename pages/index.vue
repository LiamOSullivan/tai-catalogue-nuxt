<template>
  <div class="container-fluid" style="height: 100vh">
    <header>
      <div class="row no-gutters" id="top-bar">
        <div class="col">
          <h4 class="page-head">
            <a href="/">Terrain-AI</a>
            <span> | Data Catalogue </span>
          </h4>
        </div>
        <div class="col page-head__title">
          <h4 id="page-title" class="page-head bold" style="text-align: right">
            Dataset Search
          </h4>
        </div>
      </div>
    </header>
    <div class="row no-gutters">
      <div class="col-3 m-1 p-0">
        <div class="row m-1">
          <label for="ref-buttons">Choose Reference Type</label>
          <div class="btn-group btn-group-sm m-0 p-0" name="ref-buttons">
            <button
              type="button"
              class="btn btn-outline-secondary"
              data-toggle="button"
              :class="[refFilter === '' && 'active']"
              @click.prevent="refFilter = ''"
            >
              <span class="badge bg-secondary text-white">{{
                records.length
              }}</span>
              All
            </button>
            <button
              type="button"
              class="btn btn-outline-secondary"
              :class="[refFilter === 'Spaceborne' && 'active']"
              @click.prevent="refFilter = 'Spaceborne'"
              data-toggle="button"
            >
              <span class="badge bg-secondary text-white">{{
                filterListProperties(records, { ref: "Spaceborne" }).length
              }}</span>
              Spaceborne
            </button>
            <button
              type="button"
              class="btn btn-outline-secondary"
              data-toggle="button"
              :class="[refFilter === 'Airborne' && 'active']"
              @click.prevent="refFilter = 'Airborne'"
            >
              <span class="badge bg-secondary text-white">{{
                filterListProperties(records, { ref: "Airborne" }).length
              }}</span>
              Airborne
            </button>
            <button
              type="button"
              class="btn btn-outline-secondary"
              data-toggle="button"
              :class="[refFilter === 'UAV' && 'active']"
              @click.prevent="refFilter = 'UAV'"
            >
              <span class="badge bg-secondary text-white">{{
                filterListProperties(records, { ref: "UAV" }).length
              }}</span>
              UAV
            </button>
            <button
              type="button"
              class="btn btn-outline-secondary"
              data-toggle="button"
              :class="[refFilter === 'GIS' && 'active']"
              @click.prevent="refFilter = 'GIS'"
            >
              <span class="badge bg-secondary text-white">{{
                filterListProperties(records, { ref: "GIS" }).length
              }}</span>
              GIS
            </button>
            <button
              type="button"
              class="btn btn-outline-secondary"
              data-toggle="button"
              :class="[refFilter === 'In-situ' && 'active']"
              @click.prevent="refFilter = 'In-situ'"
            >
              <span class="badge bg-secondary text-white">{{
                filterListProperties(records, { ref: "In-situ" }).length
              }}</span>
              In-situ
            </button>
            <button
              type="button"
              class="btn btn-outline-secondary"
              data-toggle="button"
              :class="[refFilter === 'Modelled' && 'active']"
              @click.prevent="refFilter = 'Modelled'"
            >
              <span class="badge bg-secondary text-white">{{
                filterListProperties(records, { ref: "Modelled" }).length
              }}</span>
              Modelled
            </button>
          </div>
        </div>
        <div class="row m-1">
          <label> Text Search</label><br />
          <dataset-search
            ds-search-placeholder="Search for a word or exact phrase..."
            class="form-control-sm"
          />
        </div>
        <div class="row m-1">
          <MapFilter @extent="filterOnExtent" />
        </div>
      </div>
      <div class="col-8 m-1 p-0">
        <div class="row m-0 mb-3 mt-3">
          <dataset
            v-slot="{ ds }"
            :ds-data="records"
            :ds-filter-fields="{
              ref: refFilter,
              name: startsWithFilter,
            }"
            :ds-sortby="[sortResourceTitle]"
            :ds-search-in="[
              'topic_cat',
              'sub_cat',
              'res_abs',
              'keyword',
              'res_title',
            ]"
            :ds-search-as="{}"
          >
            <div
              class="row justify-content-between mb-2"
              :data-page-count="ds.dsPagecount"
            >
              <div class="col-md-3 mb-2 mb-md-0">
                <label> Sort by </label><br />
                <button
                  type="button"
                  class="btn btn-sm btn-outline-secondary"
                  style="width: 100%"
                  @click="resourceTitleAsc = !resourceTitleAsc"
                >
                  Title {{ resourceTitleAsc ? "ascending ⏶" : "descending ⏷" }}
                </button>
              </div>
              <div class="col-md-3 mb-2 mb-md-0">
                <label for="dataset-show"> Results per page </label><br />
                <dataset-show name="dataset-show" />
              </div>
            </div>

            <div class="row">
              <div class="col-md-12">
                <dataset-item
                  class="form-row mb-3"
                  style="overflow-y: auto; max-height: 60vh"
                >
                  <template #default="{ row, rowIndex }">
                    <div class="col-md-12">
                      <div class="card mb-2">
                        <div class="card-body pt-3 pb-2 px-3">
                          <h5
                            class="card-title text-truncate mb-2"
                            :title="`Index: ${rowIndex}`"
                          >
                            {{ row.getProperties()["res_title"] }}
                          </h5>
                          <h6 class="card-subtitle text-truncate text-muted">
                            {{ row.getProperties()["ref"] }} |
                            {{
                              row.getProperties()["topic_cat"] !== "Other"
                                ? row.getProperties()["topic_cat"]
                                : "No Specific Terrain"
                            }}
                            |
                            {{ row.getProperties()["sub_cat"] }}
                          </h6>
                          <small class="card-text mb-0">
                            {{ row.getProperties()["res_abs"] }}
                          </small>
                          <p class="card-text text-truncate text-right">
                            {{ row.getProperties()["date_from"] }} to
                            {{ row.getProperties()["date_to"] }}
                          </p>
                          <div class="row justify-content-end">
                            <div
                              class="
                                col-md-6
                                mb-2 mb-md-0
                                d-flex
                                justify-content-end
                              "
                            >
                              <button
                                type="button"
                                class="btn btn-sm btn-outline-primary m-1"
                                style="width: 33%"
                                @click.prevent="gotoDetails(row)"
                              >
                                Details
                              </button>

                              <button
                                type="button"
                                class="btn btn-sm btn-outline-info m-1"
                                style="width: 33%"
                                :disabled="!getViewerUrl(row)"
                                @click="openLink(getViewerUrl(row))"
                              >
                                View Data
                                <font-awesome-icon
                                  icon="fa-solid fa-up-right-from-square"
                                />
                              </button>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </template>
                  <template #noDataFound>
                    <div class="col-md-12 pt-2">
                      <p class="text-center">No results found</p>
                    </div>
                  </template>
                </dataset-item>
              </div>
            </div>
            <div
              class="
                d-flex
                flex-md-row flex-column
                justify-content-between
                align-items-center
              "
            >
              <dataset-info class="mb-2 mb-md-0" />
              <dataset-pager class="pagination-sm" />
            </div>
          </dataset>
        </div>
      </div>
    </div>
  </div>
</template>


<script lang="ts">
import Vue from "vue";
import axios from "axios";
import {
  filterListProperties,
  //   isoDateToEuroDate,
  //   searchAsEuroDate,
} from "../utilities/search";

import "bootstrap/dist/css/bootstrap.css";
import { library } from "@fortawesome/fontawesome-svg-core";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { faUpRightFromSquare } from "@fortawesome/free-solid-svg-icons";
import MapFilter from "~/components/MapFilter.vue";
import GeoJSON from "ol/format/GeoJSON";
import Vector from "ol/source/Vector";

import {
  Dataset,
  DatasetItem,
  DatasetInfo,
  DatasetPager,
  DatasetSearch,
  DatasetShow,
} from "vue-dataset";

library.add(faUpRightFromSquare);
Vue.component("font-awesome-icon", FontAwesomeIcon);
Vue.config.productionTip = false;
let cache: any[] = [];

export default Vue.extend({
  name: "DataCatalogue",
  components: {
    Dataset,
    DatasetItem,
    DatasetInfo,
    DatasetPager,
    DatasetSearch,
    DatasetShow,
    MapFilter,
  },
  data: () => {
    const data: {
      records: any[];
      startsWith: string;
      refFilter: string;
      resourceTitleAsc: boolean;
    } = { records: [], startsWith: "", refFilter: "", resourceTitleAsc: true };

    return data;
  },
  computed: {
    sortResourceTitle() {
      return this.resourceTitleAsc ? "res_title" : "-res_title";
    },
  },
  async created() {
    try {
      let baseURLJson =
        "https://tai-api.terrainai.com/api/v1/dc/dc-data?format=json";

      const res = await axios.get(`${baseURLJson}`);

      const features = new GeoJSON().readFeatures(res.data[0], {
        dataProjection: "EPSG:4326",
        featureProjection: "EPSG:3857",
      });
      features.forEach((f: any) => {
        // need to pull the keys for sorting etc. out of nested properties, as dataset methods can't traverse down
        [
          "ref",
          "res_title",
          "topic_cat",
          "sub_cat",
          "res_abs",
          "keyword",
          "name",
        ].forEach((key) => {
          f[key] = f.getProperties()[key];
        });

        this.records.push(f);
        cache.push(f); // init the cache
      });
      console.log(this.records[0]);
    } catch (error) {
      console.log("error fetching data " + error);
    }
  },
  mounted() {
    // override some vue-dataset defaults
    // let showFormSelect: any = document.querySelector(
    //   "#__layout > div > div > div:nth-child(3) > div.row.justify-content-between.mb-2 > div:nth-child(2) > div > select"
    // );
    // showFormSelect.classList.add("form-control-sm");
    // let showFormLabelPre: any = document.querySelector(
    //   "#__layout > div > div > div:nth-child(3) > div.row.justify-content-between.mb-2 > div:nth-child(2) > div > label:nth-child(1)"
    // );
    // showFormLabelPre.style.display = "none";
    // let showFormLabelPost: any = document.querySelector(
    //   "#__layout > div > div > div:nth-child(3) > div.row.justify-content-between.mb-2 > div:nth-child(2) > div > label:nth-child(3)"
    // );
    // showFormLabelPost.style.display = "none";
  },

  methods: {
    filterListProperties,

    startsWithFilter(value: string) {
      return value.toLowerCase().startsWith(this.startsWith.toLowerCase());
    },
    getViewerUrl(row: any) {
      // TODO: do empty table values return undefined?
      const hasUrl =
        row.getProperties()["data_viewer"] !== null &&
        row.getProperties()["data_viewer"].toLowerCase() !== "none" &&
        row.getProperties()["data_viewer"].toLowerCase() !== "tbd" &&
        row.getProperties()["data_viewer"].toLowerCase() !== "tbc"
          ? row.getProperties()["data_viewer"].toString()
          : false;
      return hasUrl;
    },
    openLink(url: string) {
      window.open(url);
    },
    gotoDetails(row: any) {
      const rowName = row["res_title"]
        .toString()
        .trim()
        .replaceAll(" ", "-")
        .replaceAll("/", "-");

      const id = parseInt(row.getProperties()["dc_id"]);

      // this.$router.push({ name: `details-name`, params: { name: rowName } }); // this loses state when naved back to
      window.history.pushState({}, "");
      window.location.href = `/details/${id}`;
    },
    filterOnExtent(extent: any) {
      if (extent) {
        const recordsSource = new Vector({
          features: this.records,
        });
        let recordsIxFeatures: any[] = [];

        recordsSource.forEachFeatureIntersectingExtent(extent, (f) => {
          recordsIxFeatures.push(f);
          return false;
        });
        cache = this.records;
        this.records = recordsIxFeatures;
      } else {
        this.records = cache;
      }
    },
    print(text: string) {
      console.log(text);
    },
  },
});
</script>
<style scoped>
h2,
h3,
h4,
h5,
h6,
label {
  font-weight: 400;
  color: #014356;
}
label {
  padding-bottom: 12px;
}

p,
ul,
li {
  font-weight: 300;
  color: #014356;
}
small {
  font-weight: 300;
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
}
.no-gutters {
  margin-right: 0;
  margin-left: 0;
}

.btn-group {
  max-width: 100%;
  display: block;
}
.btn-group .btn {
  margin-bottom: 16px;
  border-radius: 4px 4px 4px 4px;
}
.btn-group .btn.active {
  margin-bottom: 16px;
  border-radius: 4px 4px 4px 4px;
  background-color: rgba(1, 67, 86, 0.85);
}

.btn-group > .btn:not(:last-child):not(.dropdown-toggle),
.btn-group > .btn.dropdown-toggle-split:first-child,
.btn-group > .btn-group:not(:last-child) > .btn {
  border-top-right-radius: 4px;
  border-bottom-right-radius: 4px;
}

.btn-group .badge {
  position: absolute;
  top: -16px;
  right: -4px;
  z-index: 10;
  background-color: #014356;
}

.bg-secondary {
  --bs-bg-opacity: 1;
  /* background-color: rgba(108, 117, 125, var(--bs-bg-opacity)) !important; */
  background-color: rgba(1, 67, 86, 0.85) !important;
}

.card {
  width: 100%;
}
</style>
