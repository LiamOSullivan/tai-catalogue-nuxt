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
      <div class="col-md-6 mb-1 mb-md-0" style="width: 100%; height: 400px">
        <MapContainer />
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
              filterList(records, { Reference: "Spaceborne" }).length
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
              filterList(records, { Reference: "Airborne" }).length
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
              filterList(records, { Reference: "UAV" }).length
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
              filterList(records, { Reference: "GIS" }).length
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
              filterList(records, { Reference: "In-situ" }).length
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
              filterList(records, { Reference: "Modelled" }).length
            }}</span>
            Modelled
          </button>
        </div>
      </div>
    </div>
    <div class="row mb-1">
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
    </div>

    <!-- <hr class="mb-1" /> -->

    <!-- <hr /> -->
    <dataset
      v-slot="{ ds }"
      :ds-data="records"
      :ds-filter-fields="{
        Reference: refFilter,
        'Site name': startsWithFilter,
      }"
      :ds-sortby="[sortResourceTitle]"
      :ds-search-in="[
        'Topic category',
        'Sub Category',
        'Resource abstract',
        'Keyword',
      ]"
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
                      {{ row["Resource title"] }}
                    </h5>
                    <h6 class="card-subtitle text-truncate text-muted">
                      {{ row["Reference"] }} |
                      {{
                        row["Topic category"] !== "Other"
                          ? row["Topic category"]
                          : "No Specific Terrain"
                      }}
                      |
                      {{ row["Sub Category"] }}
                    </h6>
                    <small class="card-text mb-0">
                      {{ row["Resource abstract"] }}
                    </small>
                    <p class="card-text text-truncate text-right">
                      {{ row["Temporal reference"] }}
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
import {
  filterList,
  //   isoDateToEuroDate,
  //   searchAsEuroDate,
} from "../utilities";
import { debounce } from "../helpers";
import Papa from "papaparse";
import "bootstrap/dist/css/bootstrap.css";
import { library } from "@fortawesome/fontawesome-svg-core";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { faUpRightFromSquare } from "@fortawesome/free-solid-svg-icons";
import MapContainer from "~/components/MapContainer.vue";

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

export default Vue.extend({
  name: "DataCatalogue",
  components: {
    Dataset,
    DatasetItem,
    DatasetInfo,
    DatasetPager,
    DatasetSearch,
    DatasetShow,
    MapContainer,
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
  created() {
    const startWithInput = debounce((e: any) => {
      this.startsWith = e.target.value;
    }, 500);

    const self = this;
    try {
      const useGeoSearch = true;
      let url = "https://tai-api.terrainai.com/api/v1/dc/dc-data/";
      if (useGeoSearch) {
        url =
          "https://tai-api.terrainai.com/api/v1/dc/dc-data-geo/?search_area=SRID%3D4326%3BMULTIPOLYGON(((-6.7731%2053.6116%2C-6.7707%2053.7014%2C-6.6181%2053.6999%2C-6.6208%2053.6101%2C-6.7731%2053.6116)))&project=tai&format=csv";
        console.log("use geo search");
      }
      Papa.parse(url, {
        download: true,
        header: true,
        complete: function (results) {
          if (results && results.errors) {
            if (results.errors.length > 0) {
              console.log("Results errors: ", results.errors);
            }
            if (results.data && results.data.length > 0) {
              console.log("Results:", results);
              self.records = results.data;
            }
          }
        },
      });
    } catch (error) {
      console.log("error fetching data " + error);
    }
  },
  mounted() {
    // override some vue-dataset defaults
    let showFormSelect: any = document.querySelector(
      "#__layout > div > div:nth-child(4) > div.row.justify-content-between.mb-2 > div:nth-child(2) > div > select"
    );
    showFormSelect.classList.add("form-control-sm");
    let showFormLabelPre: any = document.querySelector(
      "#__layout > div > div:nth-child(4) > div.row.justify-content-between.mb-2 > div:nth-child(2) > div > label:nth-child(1)"
    );
    showFormLabelPre.style.display = "none";
    let showFormLabelPost: any = document.querySelector(
      "#__layout > div > div:nth-child(4) > div.row.justify-content-between.mb-2 > div:nth-child(2) > div > label:nth-child(3)"
    );
    // showFormLabelPost.style.display = "none";
  },
  computed: {
    sortResourceTitle() {
      return this.resourceTitleAsc ? "Resource title" : "-Resource title";
    },
  },
  methods: {
    filterList,
    startsWithFilter(value: string) {
      return value.toLowerCase().startsWith(this.startsWith.toLowerCase());
    },
    getViewerUrl(row: any) {
      // TODO: do empty table values return undefined?
      const hasUrl =
        row["data_viewer (not an INSPIRE Column)"] !== null &&
        row["data_viewer (not an INSPIRE Column)"].toLowerCase() !== "none"
          ? row["data_viewer (not an INSPIRE Column)"].toString()
          : false;
      return hasUrl;
    },
    openLink(url: string) {
      window.open(url);
    },
    gotoDetails(row: any) {
      const rowName = row["Resource title"]
        .toString()
        .trim()
        .replaceAll(" ", "-");

      // this.$router.push({ name: `details-name`, params: { name: rowName } }); // this loses state when naved back to
      window.location.href = `/details/${rowName}`;
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
