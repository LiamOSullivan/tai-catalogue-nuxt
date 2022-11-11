<template>
  <div class="row" style="width: 100%; min-height: inherit; height: inherit">
    <div
      class="col-md-12 mb-1 mb-md-0"
      style="width: 100%; min-height: inherit; height: inherit"
    >
      <label for="map-root">Dataset Spatial Extent</label>
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
import Draw from "ol/interaction/Draw";
import ExtentInteraction from "ol/interaction/Extent";
// import Cluster from "ol/source/Cluster";
import GeoJSON from "ol/format/GeoJSON";
import { Style, Fill, Stroke, Circle, Text } from "ol/style";
import "ol/ol.css";
// import { loadMetStations } from "~/utils/loadMetStations";

export default Vue.extend({
  name: "MapContainer",
  data: () => {
    let extent = {};
    return extent;
  },
  async mounted() {
    const self = this;
    const drawSource = new VectorSource({ wrapX: false });

    const drawVector = new VectorLayer({
      source: drawSource,
    });

    const map = new Map({
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
        drawVector,
      ],

      view: new View({
        center: [-900000, 7046600], // default: Ireland
        zoom: 8, // default countrywide,
        constrainResolution: true,
      }),
    });
    console.log(this.$refs);
    map.setTarget(this.$refs["map-root"]);
    const extentInteraction = new ExtentInteraction({});
    map.addInteraction(extentInteraction);

    extentInteraction.on("extentend", (listener) => {
      console.log("extentchanged");
    });

    let extent = extentInteraction.getExtent();
    if (extent) {
      const poly = new Polygon(extent);
      let geojson = new GeoJSON().writeFeatures([new Feature()]);
      console.log(geojson);
    }

    let draw = new Draw({
      source: drawSource,
      type: "Polygon",
    });
    // map.addInteraction(draw);

    const hightlightStroke = new Stroke({
      color: "rgba(255, 255, 255, 0.7)",
      width: 2,
    });

    const highlightCircle = new Circle({
      radius: 10,
      stroke: new Stroke({
        color: "red",
        width: 2,
      }),
    });

    const highlightStyle = new Style({
      stroke: hightlightStroke,
      image: highlightCircle,
    });

    // const source = new VectorSource();

    // const featureOverlay = new VectorLayer({
    //   source,
    //   map: map,
    //   style: highlightStyle,
    // });

    // const hoverOverlay = await getHoverOverlayFeature(map);
    let highlight;

    map.on("pointermove", function (evt) {
      if (evt.dragging) {
        return true;
      }
      // const pixel = map.getEventPixel(evt.originalEvent);
      // displayFeatureInfo(pixel);
    });

    map.on("click", function (evt) {
      // displayFeatureInfo(evt.pixel);
      // const pixel = map.getEventPixel(evt.originalEvent);
      // map.getFeaturesAtPixel(pixel).forEach((f) => {
      //   if (f.getProperties().dataset_type === "soil_moisture") {
      //     window.location.href = "../charts/soils/" + f.getProperties().tai_id;
      //   } else {
      //     window.location.href = "../charts/met/" + f.getProperties().tai_id;
      //   }
      // });
    });
  },
  methods: {
    emitExtent: () => {},
  },
});
</script>
