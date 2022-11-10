<template>
  <div ref="map-root" style="width: 100%; height: 90%" />
</template>

<script>
import View from "ol/View";
import Map from "ol/Map";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import VectorLayer from "ol/layer/Vector";
import VectorSource from "ol/source/Vector";
// import Cluster from "ol/source/Cluster";
import GeoJSON from "ol/format/GeoJSON";
import { Style, Fill, Stroke, Circle, Text } from "ol/style";
import "ol/ol.css";
// import { loadMetStations } from "~/utils/loadMetStations";
import axios from "axios";
// import { getHoverOverlayFeature } from "~/utils/map-ui.ts";
// import { computed } from "@vue/composition-api";
// import Message from "~/models/message";

export default {
  name: "MapContainer",
  async mounted() {
    const netMap = new Map({
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
    netMap.setTarget(this.$refs["map-root"]);

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

    const source = new VectorSource();

    const featureOverlay = new VectorLayer({
      source,
      map: netMap,
      style: highlightStyle,
    });

    // const hoverOverlay = await getHoverOverlayFeature(netMap);
    let highlight;

    netMap.on("pointermove", function (evt) {
      if (evt.dragging) {
        return true;
      }
      const pixel = netMap.getEventPixel(evt.originalEvent);
      // displayFeatureInfo(pixel);
    });

    netMap.on("click", function (evt) {
      // displayFeatureInfo(evt.pixel);
      const pixel = netMap.getEventPixel(evt.originalEvent);

      netMap.getFeaturesAtPixel(pixel).forEach((f) => {
        if (f.getProperties().dataset_type === "soil_moisture") {
          window.location.href = "../charts/soils/" + f.getProperties().tai_id;
        } else {
          window.location.href = "../charts/met/" + f.getProperties().tai_id;
        }
      });
    });
  },
};
</script>
