<template>
  <div class="row" style="width: 100%; min-height: inherit; height: inherit">
    <div
      class="col-md-12 mb-1 mb-md-0"
      style="width: 100%; min-height: inherit; height: inherit"
    >
      <div name="map-root" ref="map-root" style="width: 100%; height: 90%" />
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import View from "ol/View";
import Map from "ol/Map";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import VectorLayer from "ol/layer/Vector";
import VectorSource from "ol/source/Vector";
import Feature from "ol/Feature";
import Polygon from "ol/geom/Polygon";
import { fromExtent } from "ol/geom/Polygon";
import GeoJSON from "ol/format/GeoJSON";
import { Style, Fill, Stroke, Circle, Text } from "ol/style";
import "ol/ol.css";
// import { loadMetStations } from "~/utils/loadMetStations";
let map;
export default Vue.extend({
  name: "MapBBox",
  props: ["poly"],
  data: () => {
    return {};
  },
  created() {},
  mounted() {
    map = new Map({
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
      ],

      view: new View({
        center: [-900000, 7046600], // default: Ireland
        zoom: 8, // default countrywide,
        constrainResolution: true,
      }),
    });
    map.setTarget(this.$refs["map-root"]);
    console.log("MapBbox poly: ", this.poly);
    const bboxStroke = new Stroke({
      color: "rgba(255, 0, 0, 0.7)",
      width: 2,
    });
    const bboxCircle = new Circle({
      radius: 10,
      stroke: new Stroke({
        color: "red",
        width: 2,
      }),
    });
    const bboxStyle = new Style({
      stroke: bboxStroke,
      image: bboxCircle,
    });
    const bboxFeature = new Feature(this.poly);
    const bboxSource = new VectorSource({ wrapX: false });
    bboxSource.addFeature(bboxFeature);
    const bbox = new VectorLayer({
      source: bboxSource,
    });
    bbox.setStyle(bboxStyle);
    map.addLayer(bbox);
    map.getView().fit(this.poly, { padding: [50, 50, 50, 50] });
  },
  methods: {},
});
</script>
