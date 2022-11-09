<template>
  <div ref="map-root" style="width: 100%; height: 100%" />
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
    const soilsSites = {
      features: [
        {
          geometry: {
            coordinates: [-6.4968738, 52.297713],
            type: "Point",
          },
          properties: {
            attributes: {
              Variables: [
                {
                  Title: "Temperature",
                  "Unit of measurement": "Celcius",
                },
                {
                  Title: "Dielectric permittivity",
                  "Unit of measurement": "farads per meter",
                },
                {
                  Title: "Soil Moisture",
                  "Unit of measurement": "m3/m3 (water/soil)",
                },
              ],
            },
            id: 1,
            tai_id: 8,
            title: "JC Node 1",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.5010638, 52.2972437],
            type: "Point",
          },
          properties: {
            attributes: {
              Variables: [
                {
                  Title: "Temperature",
                  "Unit of measurement": "Celcius",
                },
                {
                  Title: "Dielectric permittivity",
                  "Unit of measurement": "farads per meter",
                },
                {
                  Title: "Soil Moisture",
                  "Unit of measurement": "m3/m3 (water/soil)",
                },
              ],
            },
            id: 2,
            tai_id: 8,
            title: "JC Node 2",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.4971273, 52.298258],
            type: "Point",
          },
          properties: {
            attributes: {
              Variables: [
                {
                  Title: "Temperature",
                  "Unit of measurement": "Celcius",
                },
                {
                  Title: "Dielectric permittivity",
                  "Unit of measurement": "farads per meter",
                },
                {
                  Title: "Soil Moisture",
                  "Unit of measurement": "m3/m3 (water/soil)",
                },
              ],
            },
            id: 3,
            tai_id: 8,
            title: "JC Node 3",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.4973875, 52.2995452],
            type: "Point",
          },
          properties: {
            attributes: {
              Variables: [
                {
                  Title: "Temperature",
                  "Unit of measurement": "Celcius",
                },
                {
                  Title: "Dielectric permittivity",
                  "Unit of measurement": "farads per meter",
                },
                {
                  Title: "Soil Moisture",
                  "Unit of measurement": "m3/m3 (water/soil)",
                },
              ],
            },
            id: 4,
            tai_id: 8,
            title: "JC Node 4",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.5008623, 52.2997743],
            type: "Point",
          },
          properties: {
            attributes: {
              Variables: [
                {
                  Title: "Temperature",
                  "Unit of measurement": "Celcius",
                },
                {
                  Title: "Dielectric permittivity",
                  "Unit of measurement": "farads per meter",
                },
                {
                  Title: "Soil Moisture",
                  "Unit of measurement": "m3/m3 (water/soil)",
                },
              ],
            },
            id: 5,
            tai_id: 8,
            title: "JC Node 5",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.4998129, 52.2981184],
            type: "Point",
          },
          properties: {
            attributes: {
              Variables: [
                {
                  Title: "Temperature",
                  "Unit of measurement": "Celcius",
                },
                {
                  Title: "Dielectric permittivity",
                  "Unit of measurement": "farads per meter",
                },
                {
                  Title: "Soil Moisture",
                  "Unit of measurement": "m3/m3 (water/soil)",
                },
              ],
            },
            id: 6,
            tai_id: 8,
            title: "JC Node 6",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.4983122, 52.2986131],
            type: "Point",
          },
          properties: {
            attributes: {
              Variables: [
                {
                  Title: "Temperature",
                  "Unit of measurement": "Celcius",
                },
                {
                  Title: "Dielectric permittivity",
                  "Unit of measurement": "farads per meter",
                },
                {
                  Title: "Soil Moisture",
                  "Unit of measurement": "m3/m3 (water/soil)",
                },
              ],
            },
            id: 7,
            tai_id: 8,
            title: "JC Node 7",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.499083, 52.2990029],
            type: "Point",
          },
          properties: {
            attributes: {
              Variables: [
                {
                  Title: "Temperature",
                  "Unit of measurement": "Celcius",
                },
                {
                  Title: "Dielectric permittivity",
                  "Unit of measurement": "farads per meter",
                },
                {
                  Title: "Soil Moisture",
                  "Unit of measurement": "m3/m3 (water/soil)",
                },
              ],
            },
            id: 8,
            tai_id: 8,
            title: "JC Node 8",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.4964226, 52.2959876],
            type: "Point",
          },
          properties: {
            attributes: {
              Variables: [
                {
                  Title: "Temperature",
                  "Unit of measurement": "Celcius",
                },
                {
                  Title: "Dielectric permittivity",
                  "Unit of measurement": "farads per meter",
                },
                {
                  Title: "Soil Moisture",
                  "Unit of measurement": "m3/m3 (water/soil)",
                },
              ],
            },
            id: 9,
            tai_id: 8,
            title: "JC Node 9",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.499556, 52.2995231],
            type: "Point",
          },
          properties: {
            attributes: {
              Variables: [
                {
                  Title: "Temperature",
                  "Unit of measurement": "Celcius",
                },
                {
                  Title: "Dielectric permittivity",
                  "Unit of measurement": "farads per meter",
                },
                {
                  Title: "Soil Moisture",
                  "Unit of measurement": "m3/m3 (water/soil)",
                },
              ],
            },
            id: 10,
            tai_id: 8,
            title: "JC Node 10",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.660742, 53.516083],
            type: "Point",
          },
          properties: {
            attributes: {
              details: [
                {
                  "Conditions for access and use": "Free to use within T-AI",
                  Conformity: "",
                  "Geographic bounding box": "",
                  Instrument: "SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  Keyword: "",
                  "Limitations on public access":
                    "Data is produced by Terrain-AI and is restricted to Terrain-AI project researchers and approved collaborators.",
                  Lineage:
                    "Data from SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  "Metadata date": "15-06-2022",
                  "Metadata language": "English",
                  "Metadata point of contact": "data.terrainai@mu.ie",
                  "Resource Abstract":
                    "In-situ soil moisture data for Grange using SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  "Resource Title": "In-situ soil moisture data",
                  "Resource Type": "Dataset",
                  "Resource language": "",
                  "Resource locator": "",
                  "Responsible organisation": "Terrain-AI",
                  "Spatial resolution": "",
                  "Temporal reference": "21-02-2021",
                  "Topic category": "",
                  "Unique resource identifier": "",
                },
              ],
            },
            id: 11,
            tai_id: 18,
            title: "Dunsany",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-7.313896, 54.053062],
            type: "Point",
          },
          properties: {
            attributes: {
              details: [
                {
                  "Conditions for access and use": "Free to use within T-AI",
                  Conformity: "",
                  "Geographic bounding box": "",
                  Instrument: "SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  Keyword: "",
                  "Limitations on public access":
                    "Data is produced by Terrain-AI and is restricted to Terrain-AI project researchers and approved collaborators.",
                  Lineage:
                    "Data from SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  "Metadata date": "15-06-2022",
                  "Metadata language": "English",
                  "Metadata point of contact": "data.terrainai@mu.ie",
                  "Resource Abstract":
                    "In-situ soil moisture data for Ballyhaise using SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  "Resource Title": "In-situ soil moisture data",
                  "Resource Type": "Dataset",
                  "Resource language": "",
                  "Resource locator": "",
                  "Responsible organisation": "Terrain-AI",
                  "Spatial resolution": "",
                  "Temporal reference": "21-02-2021",
                  "Topic category": "",
                  "Unique resource identifier": "",
                },
              ],
            },
            id: 12,
            tai_id: 19,
            title: "Ballyhaise",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-8.992195, 53.710762],
            type: "Point",
          },
          properties: {
            attributes: {
              details: [
                {
                  "Conditions for access and use": "Free to use within T-AI",
                  Conformity: "",
                  "Geographic bounding box": "",
                  Instrument: "SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  Keyword: "",
                  "Limitations on public access":
                    "Data is produced by Terrain-AI and is restricted to Terrain-AI project researchers and approved collaborators.",
                  Lineage:
                    "Data from SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  "Metadata date": "15-06-2022",
                  "Metadata language": "English",
                  "Metadata point of contact": "data.terrainai@mu.ie",
                  "Resource Abstract":
                    "In-situ soil moisture data for Claremorris using SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  "Resource Title": "In-situ soil moisture data",
                  "Resource Type": "Dataset",
                  "Resource language": "",
                  "Resource locator": "",
                  "Responsible organisation": "Terrain-AI",
                  "Spatial resolution": "",
                  "Temporal reference": "21-02-2021",
                  "Topic category": "",
                  "Unique resource identifier": "",
                },
              ],
            },
            id: 13,
            tai_id: 20,
            title: "Claremorris",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-8.785531, 53.289134],
            type: "Point",
          },
          properties: {
            attributes: {
              details: [
                {
                  "Conditions for access and use": "Free to use within T-AI",
                  Conformity: "",
                  "Geographic bounding box": "",
                  Instrument: "SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  Keyword: "",
                  "Limitations on public access":
                    "Data is produced by Terrain-AI and is restricted to Terrain-AI project researchers and approved collaborators.",
                  Lineage:
                    "Data from SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  "Metadata date": "15-06-2022",
                  "Metadata language": "English",
                  "Metadata point of contact": "data.terrainai@mu.ie",
                  "Resource Abstract":
                    "In-situ soil moisture data for Athenry using SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  "Resource Title": "In-situ soil moisture data",
                  "Resource Type": "Dataset",
                  "Resource language": "",
                  "Resource locator": "",
                  "Responsible organisation": "Terrain-AI",
                  "Spatial resolution": "",
                  "Temporal reference": "21-02-2021",
                  "Topic category": "",
                  "Unique resource identifier": "",
                },
              ],
            },
            id: 14,
            tai_id: 21,
            title: "Athenry",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-6.497443, 52.299452],
            type: "Point",
          },
          properties: {
            attributes: {
              details: [
                {
                  "Conditions for access and use": "Free to use within T-AI",
                  Conformity: "",
                  "Geographic bounding box": "",
                  Instrument: "SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  Keyword: "",
                  "Limitations on public access":
                    "Data is produced by Terrain-AI and is restricted to Terrain-AI project researchers and approved collaborators.",
                  Lineage:
                    "Data from SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  "Metadata date": "15-06-2022",
                  "Metadata language": "English",
                  "Metadata point of contact": "data.terrainai@mu.ie",
                  "Resource Abstract":
                    "In-situ soil moisture data for Johnstown Castle using SoilVUE10 TDR soil moisture profile sensor 1.0m",
                  "Resource Title": "In-situ soil moisture data",
                  "Resource Type": "Dataset",
                  "Resource language": "",
                  "Resource locator": "",
                  "Responsible organisation": "Terrain-AI",
                  "Spatial resolution": "",
                  "Temporal reference": "21-02-2021",
                  "Topic category": "",
                  "Unique resource identifier": "",
                },
              ],
            },
            id: 15,
            tai_id: 8,
            title: "Johnstown Castle",
          },
          type: "Feature",
        },
        {
          geometry: {
            coordinates: [-10.241168, 51.938192],
            type: "Point",
          },
          properties: {
            // attributes: {
            //   details: [
            //     {
            //       "Conditions for access and use": "Free to use within T-AI",
            //       Conformity: "",
            //       "Geographic bounding box": "",
            //       Instrument: "SoilVUE10 TDR soil moisture profile sensor 1.0m",
            //       Keyword: "",
            //       "Limitations on public access":
            //         "Data is produced by Terrain-AI and is restricted to Terrain-AI project researchers and approved collaborators.",
            //       Lineage:
            //         "Data from SoilVUE10 TDR soil moisture profile sensor 1.0m",
            //       "Metadata date": "15-06-2022",
            //       "Metadata language": "English",
            //       "Metadata point of contact": "data.terrainai@mu.ie",
            //       "Resource Abstract":
            //         "In-situ soil moisture data for Valentia using SoilVUE10 TDR soil moisture profile sensor 1.0m",
            //       "Resource Title": "In-situ soil moisture data",
            //       "Resource Type": "Dataset",
            //       "Resource language": "",
            //       "Resource locator": "",
            //       "Responsible organisation": "Terrain-AI",
            //       "Spatial resolution": "",
            //       "Temporal reference": "21-02-2021",
            //       "Topic category": "",
            //       "Unique resource identifier": "",
            //     },
            //   ],
            // },
            id: 16,
            tai_id: 22,
            title: "Valentia",
          },
          type: "Feature",
        },
      ],
      type: "FeatureCollection",
    };

    const soilsFeatures = new GeoJSON().readFeatures(soilsSites, {
      featureProjection: "EPSG:3857",
    });

    soilsFeatures.forEach((f) => {
      f.setProperties({ dataset_type: "soil_moisture" });
    });

    const ciSoils = new Circle({
      radius: 8,
      stroke: new Stroke({
        color: "black",
        width: 2,
      }),
      fill: new Fill({
        color: "rgba(255, 255, 255, 0.8)",
      }),
    });

    const soilsLayer = new VectorLayer({
      source: new VectorSource({
        features: soilsFeatures,
      }),
      style: new Style({
        image: ciSoils,
        text: new Text({
          text: "S",
          fill: new Fill({
            color: "black",
          }),
        }),
      }),
    });

    const ciWeather = new Circle({
      radius: 8,
      stroke: new Stroke({
        color: "black",
        width: 2,
      }),
      fill: new Fill({
        color: "rgba(255, 255, 255, 0.8)",
      }),
    });

    const response = await axios.get(
      "https://tai-api.terrainai.com/api/v1/weather/sites"
    );

    const weatherSites = response.data[0];

    const weatherFeatures = new GeoJSON().readFeatures(weatherSites, {
      featureProjection: "EPSG:3857",
    });
    weatherFeatures.forEach((f) => {
      f.setProperties();
    });

    const weatherLayer = new VectorLayer({
      source: new VectorSource({
        features: weatherFeatures,
      }),
      style: new Style({
        image: ciWeather,
        text: new Text({
          text: "W",
          fill: new Fill({
            color: "black",
          }),
        }),
      }),
    });

    const netMap = new Map({
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
        weatherLayer,
        soilsLayer,
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
    const displayFeatureInfo = function (pixel) {
      if (netMap.getFeaturesAtPixel(pixel).length === 0) {
        featureOverlay.getSource().clear(true);
        highlight = null;
      }

      netMap.getFeaturesAtPixel(pixel).forEach((f) => {
        if (f !== highlight) {
          if (highlight) {
            featureOverlay.getSource().clear(true);
          }
          if (f) {
            featureOverlay.getSource().addFeature(f);
            // let siteLabel = f.get("name") ? f.get("name") : f.get("title");
            // // : f.values_.features[0].values_.title;

            // siteLabel = siteLabel.replaceAll("_", " ");
            // const props = f.getProperties();
            // let propsHTML = ``;
            // for (const key in props) {
            //   if (
            //     Object.hasOwnProperty.call(props, key) &&
            //     key !== "geometry"
            //   ) {
            //     propsHTML += `${key}: ${props[key]} <br>`;
            //     // if (Array.isArray(props[key])) {
            //     //   propsHTML += "* len: " + props[key].length + "<br>";
            //     //   props[key].forEach((p) => {
            //     //     for (const pkey in p) {
            //     //       if (Object.hasOwnProperty.call(p, pkey)) {
            //     //         propsHTML += `${pkey}: ${props[pkey]} <br>`;
            //     //       }
            //     //     }
            //     //   });
            //     // }
            //   }
            // }
            // console.log(siteLabel);
            // console.log(propsHTML);
            // info.innerHTML = "<h3>" + siteLabel + "<h3>" + propsHTML;
          }
          highlight = f;
        }
      });
    };

    netMap.on("pointermove", function (evt) {
      if (evt.dragging) {
        return true;
      }
      const pixel = netMap.getEventPixel(evt.originalEvent);
      displayFeatureInfo(pixel);
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
