<template>
  <div class="container" style="height: 100vh">
    <!--
    <div class="row mb-4">
      <div class="col">
        <button type="button" class="btn btn-primary" @click="updateData">Update data</button>
      </div>
    </div>
    -->

    <div class="row mb-1">
      <div class="col-md-6 mb-2 mb-md-0">
        <label for="ref-buttons">Choose Reference Type</label>
        <div class="btn-group" name="ref-buttons">
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
          class="form-control"
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
            class="btn btn-outline-secondary"
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
        <dataset-pager />
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

import {
  Dataset,
  DatasetItem,
  DatasetInfo,
  DatasetPager,
  DatasetSearch,
  DatasetShow,
} from "vue-dataset";

export default Vue.extend({
  name: "DataCatalogue",
  components: {
    Dataset,
    DatasetItem,
    DatasetInfo,
    DatasetPager,
    DatasetSearch,
    DatasetShow,
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
      const url = "https://tai-api.terrainai.com/api/v1/dc/dc-data/";
      Papa.parse(url, {
        download: true,
        header: true,
        complete: function (results) {
          if (results && results.errors) {
            if (results.errors) {
              console.log("results errors: " + results.errors);
            }
            if (results.data && results.data.length > 0) {
              // console.log("Results:", results);
              self.records = results.data;
            }
          }
        },
      });
    } catch (error) {
      console.log("error fetching data " + error);
    }
  },
  mounted() {},
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
