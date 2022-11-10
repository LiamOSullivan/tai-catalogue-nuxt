<template>
  <div class="container" style="height: 100vh">
    <!--
    <div class="row mb-4">
      <div class="col">
        <button type="button" class="btn btn-primary" @click="updateData">Update data</button>
      </div>
    </div>
    -->
    <div class="row mb-3 mt-3">
      <div
        class="col-md-12 mb-1 mb-md-0"
        style="width: 100%; min-height: 400px"
      >
        <MapFilter @extent="filterOnExtent" />
      </div>
    </div>

    <div class="row mb-1">
      <div class="col-md-6 mb-2 mb-md-0">
        <label for="ref-buttons">Choose Reference Type</label>
        <div class="btn-group btn-group-sm" name="ref-buttons">
          <button
            type="button"
            class="btn btn-outline-secondary"
            :class="[refFilter === '']"
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
            :class="[refFilter === 'Spaceborne']"
            @click.prevent="refFilter = 'Spaceborne'"
          >
            <span class="badge bg-secondary text-white">{{
              filterListProperties(records, { ref: "Spaceborne" }).length
            }}</span>
            Spaceborne
          </button>
          <button
            type="button"
            class="btn btn-outline-secondary"
            :class="[refFilter === 'Airborne']"
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
            :class="[refFilter === 'UAV']"
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
            :class="[refFilter === 'GIS']"
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
            :class="[refFilter === 'In-situ']"
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
            :class="[refFilter === 'Modelled']"
            @click.prevent="refFilter = 'Modelled'"
          >
            <span class="badge bg-secondary text-white">{{
              filterListProperties(records, { ref: "Modelled" }).length
            }}</span>
            Modelled
          </button>
        </div>
      </div>
    </div>
    <!-- <div class="row mb-1">
      <div class="col-md-3">
        <label> Filter by Site Name </label><br />
        <input
          type="text"
          class="form-control form-control-sm"
          placeholder="Name starts with..."
          :value="startsWith"
          @input="startWithInput($event)"
        />
      </div>
    </div> -->

    <!-- <hr class="mb-1" /> -->

    <!-- <hr /> -->
    <dataset
      v-slot="{ ds }"
      :ds-data="records"
      :ds-filter-fields="{
        ref: refFilter,
        name: startsWithFilter,
      }"
      :ds-sortby="[sortResourceTitle]"
      :ds-search-in="['topic_cat', 'sub_cat', 'res_abs', 'keyword']"
      :ds-search-as="{}"
    >
      <div class="row mb-1">
        <div class="col-md-5">
          <label> Text Search</label><br />
          <dataset-search
            ds-search-placeholder="Search for a word or exact phrase..."
            class="form-control-sm"
          />
        </div>
      </div>
      <div class="row mb-2"></div>

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
                        class="col-md-6 mb-2 mb-md-0 d-flex justify-content-end"
                      >
                        <button
                          type="button"
                          class="btn btn-sm btn-outline-primary m-1"
                          style="width: 33%"
                          @click.prevent="gotoDetails(row)"
                        >
                          <!-- <NuxtLink to="/details"> Details </NuxtLink> -->
                          Details
                        </button>
                        <!-- TODO: add tooltips for diabled buttons/features -->
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
</template>


<script lang="ts">
import Vue from "vue";
import axios from "axios";
import {
  filterListProperties,
  //   isoDateToEuroDate,
  //   searchAsEuroDate,
} from "../utilities";

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
      });
      console.log(this.records[0]);
    } catch (error) {
      console.log("error fetching data " + error);
    }
  },
  mounted() {
    // override some vue-dataset defaults
    let showFormSelect: any = document.querySelector(
      "#__layout > div > div:nth-child(3) > div.row.justify-content-between.mb-2 > div:nth-child(2) > div > select"
    );
    showFormSelect.classList.add("form-control-sm");
    let showFormLabelPre: any = document.querySelector(
      "#__layout > div > div:nth-child(3) > div.row.justify-content-between.mb-2 > div:nth-child(2) > div > label:nth-child(1)"
    );
    showFormLabelPre.style.display = "none";
    let showFormLabelPost: any = document.querySelector(
      "#__layout > div > div:nth-child(3) > div.row.justify-content-between.mb-2 > div:nth-child(2) > div > label:nth-child(3)"
    );
    showFormLabelPost.style.display = "none";
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
        row.getProperties()["data_viewer"].toLowerCase() !== "none"
          ? row.getProperties()["data_viewer"].toString()
          : false;
      return hasUrl;
    },
    openLink(url: string) {
      window.open(url);
    },
    gotoDetails(row: any) {
      const rowName = row["res_title"].toString().trim().replaceAll(" ", "-");
      const id = parseInt(row.getProperties()["dc_id"]);

      // this.$router.push({ name: `details-name`, params: { name: rowName } }); // this loses state when naved back to
      window.location.href = `/details/${rowName}?id=${id}`;
    },
    filterOnExtent(extent: any) {
      console.log("got an extent to filter on " + extent);
      console.log(this.records[0]);

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
.btn-group .badge {
  position: absolute;
  top: -10px;
  right: -10px;
  z-index: 10;
}
.card {
  width: 100%;
}
</style>
