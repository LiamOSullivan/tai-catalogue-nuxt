<template>
  <div>
    <div class="row" style="width: 100%; min-height: inherit; height: inherit">
      <div class="mb-1 mb-md-0" style="min-height: 200px; height: 50vh">
        <label>Filter Results by Area</label>
        <div ref="map-root" style="width: 100%; height: 90%" />
      </div>
    </div>
    <div class="row" style="width: 100%; min-height: inherit; height: inherit">
      <div id="map-filter__controls" class="mb-1 mb-md-0">
        <!-- <div id="map-filter__controls__area">
          <label> Current Area: </label><br />
          <p>
            <small style="color: green">{{
              extent !== null ? extent : "Shift + drag on map to draw an area"
            }}</small>
          </p>
        </div> -->
        <div id="map-filter__controls__btns">
          <!-- <div style="display: inline-block">
            <label for="customSwitches"
              ><small
                >Within bounds only (exclude intersecting datasets)</small
              ></label
            >
            <input
              type="checkbox"
              class="custom-control-input"
              id="customSwitches"
              disabled
            />
          </div>
          <br /> -->
          <button
            type="button"
            class="btn btn-sm btn-outline-primary"
            @click.prevent="emitExtent()"
          >
            Apply
          </button>

          <button
            type="button"
            class="btn btn-sm btn-outline-primary"
            @click.prevent="clearExtent()"
          >
            Clear
          </button>
        </div>
      </div>
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
import { shiftKeyOnly } from "ol/events/condition";
// import Cluster from "ol/source/Cluster";
import GeoJSON from "ol/format/GeoJSON";
import { Style, Fill, Stroke, Circle, Text } from "ol/style";
import "ol/ol.css";
// import { loadMetStations } from "~/utils/loadMetStations";

let extentInteraction;
let map;
export default Vue.extend({
  name: "MapContainer",
  data: () => {
    return { extent: null };
  },
  async mounted() {
    const drawSource = new VectorSource({ wrapX: false });

    const drawVector = new VectorLayer({
      source: drawSource,
    });

    map = new Map({
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
        drawVector,
      ],

      view: new View({
        center: [-900000, 7046600], // default: Ireland
        zoom: 6, // default countrywide,
        constrainResolution: true,
      }),
    });

    map.setTarget(this.$refs["map-root"]);
    extentInteraction = new ExtentInteraction({
      condition: shiftKeyOnly,
      boxStyle: new Style({
        stroke: new Stroke({
          color: "rgba(1, 67, 86, 0.7)",
          width: 2,
        }),
      }),
    });
    map.addInteraction(extentInteraction);

    // extentInteraction.on("extentend", (listener) => {
    //   console.log("extentchanged");
    // });

    // this.extent = extentInteraction.getExtent();
    // if (extent) {
    //   const poly = new Polygon(extent);
    //   let geojson = new GeoJSON().writeFeatures([new Feature()]);
    //   console.log(geojson);
    // }

    // let draw = new Draw({
    //   source: drawSource,
    //   type: "Polygon",
    // });
    // // map.addInteraction(draw);

    // const hightlightStroke = new Stroke({
    //   color: "rgba(255, 25, 255, 0.7)",
    //   width: 2,
    // });

    // const highlightCircle = new Circle({
    //   radius: 10,
    //   stroke: new Stroke({
    //     color: "red",
    //     width: 2,
    //   }),
    // });

    // const highlightStyle = new Style({
    //   stroke: hightlightStroke,
    //   image: highlightCircle,
    // });

    // const source = new VectorSource();

    // const featureOverlay = new VectorLayer({
    //   source,
    //   map: map,
    //   style: highlightStyle,
    // });

    // const hoverOverlay = await getHoverOverlayFeature(map);
    let highlight;
    const self = this;
    map.on("pointermove", function (evt) {
      if (evt.dragging) {
        self.extent = extentInteraction.getExtent().map((e) => {
          return parseFloat(e.toFixed(2));
        });
        // console.log(self.extent);
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
    emitExtent() {
      console.log("emit extent: ", this.extent);
      this.$emit("extent", this.extent);
    },
    clearExtent() {
      map.removeInteraction(extentInteraction);
      this.extent = null;
      extentInteraction = new ExtentInteraction({
        condition: shiftKeyOnly,
      });
      map.addInteraction(extentInteraction);
      this.$emit("extent", this.extent);
    },
  },
});
</script>
<style scoped>
#map-filter__controls {
  height: inherit;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
}
#map-filter__controls__area {
  min-height: 30%;
}
#map-filter__controls__btns {
  min-height: 50%;
  gap: 8px;
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
}
.btn {
  max-width: 120px;
}

.btn-outline-primary {
  border: 1px solid #014356;
  color: #014356;
}
.btn-outline-primary:hover {
  border: 1px solid #014356;
  background-color: #014356;
  color: white;
}

label {
  margin: 0;
  padding: 0;
  padding-bottom: 8px;
}
</style>
