<template>
  <div id="sphere"></div>
</template>

<script>
import * as d3 from "d3";
import stars from "../../assets/data/stars.6.json";
import starNames from "../../assets/data/starnames.json";
import constellationLines from "../../assets/data/constellations.lines.json";
import constellations from "../../assets/data/constellationName.json";

export default {
  name: "SphereChart",
  props: {
    mySpherechartData: Array,
  },
  mounted() {
    try {
      const localData = "hi";
      this.drawSphereChart(localData, "#sphere");
      console.log("Data Passed down as a Prop  ");
    } catch (e) {
      console.log("MOUNT ERROR", e);
    }
  },
  methods: {
    drawSphereChart(localData, id) {
      const height = 1000;
      const width = height;
      const padding = 10;
      const sensitivity = 75;
      const starlist = [
        "Sirius",
        "Betelgeuse",
        "Rigel",
        "Canopus",
        "Arcturus",
        "Antares",
        "Capella",
        "Deneb",
        "Aldebaran",
        "Altair",
        "Pollux",
        "Castor",
        "Bellatrix",
        "Procyon",
        "Vega",
        "Polaris",
        "Proxima Centauri",
      ];

      const starIDs = [
        32349, 27989, 24436, 30438, 69673, 80763, 24608, 102098, 21421, 97649,
        37826, 36850, 25336, 37279, 91262, 11767, 68191,
      ];

      let starObj = {
        Sirius: {
          age: "230 Million",
          current: "Main Sequence",
          future: "Red Giant -> Planetary Nebula  -> White Dwarf",
        },
        Betelgeuse: {
          age: "10 Million",
          current: "Red Supergiant",
          future: "Supernova -> Neutron Star/Black Hole",
        },
        Rigel: {
          age: "8 Million",
          current: "White Supergiant",
          future: "Supernova -> Neutron Star/Black Hole",
        },
        Canopus: {
          age: "10 Million",
          current: "Main Sequence",
          future: "Planetary Nebula -> White Dwarf",
        },
        Arcturus: {
          age: "7.1 Billion",
          current: "Red Giant",
          future: "Planetary Nebula -> White Dwarf",
        },
        Antares: {
          age: "11.01 Million",
          current: "Red Supergiant",
          future: "Supernova -> Neutron Star/Black Hole",
        },
        Capella: {
          age: "400 Million",
          current: "Red Giant",
          future: "Evolving Red Giant",
        },
        Deneb: {
          age: "10.01 Million",
          current: "Blue Supergiant",
          future: "Supernova -> Neutron Star/Black Hole",
        },
        Aldebaran: {
          age: "6.6 Billion",
          current: "Red Giant",
          future: "Evolving Red Giant",
        },
        Altair: {
          age: "1.2 Billion",
          current: "Main Sequence",
          future: "Red Giant -> Planetary Nebula -> White Dwarf",
        },
        Pollux: {
          age: "724.5 Million",
          current: "Red Giant",
          future: "Planetary Nebula -> White Dwarf",
        },
        Castor: {
          age: "370 Million",
          current: "Main Sequence",
          future: "Red Giant -> Planetary Nebula -> White Dwarf",
        },
        Bellatrix: {
          age: "25 Million",
          current: "Main Sequence",
          future: "Giant -> Supergiant -> Supernova",
        },
        Procyon: {
          age: "1.7 Billion",
          current: "Main Sequence",
          future: "Red Giant -> Planetary Nebula -> White Dwarf",
        },
        Vega: {
          age: "455.3 Million",
          current: "Main Sequence",
          future: "Red Giant -> Planetary Nebula -> White Dwarf",
        },
        Polaris: {
          age: "70 Million",
          current: "White Dwarf",
          future: "Red Giant -> Planetary Nebula -> White Dwarf",
        },
        "Proxima Centauri": {
          age: "4.85 Billion",
          current: "Red Dwarf",
          future: "Main Sequence -> Blue Dwarf",
        },
      };

      const svg = d3
        .select(id)
        .append("svg")
        .attr("width", width)
        .attr("height", height);

      const projection = d3["geoOrthographic"]().precision(0.1);

      const initialScale = projection.scale();

      const magnitudeScale = d3
        .scaleLinear()
        .domain(d3.extent(stars.features, (d) => d.properties.mag))
        .range([3, 0.5]);

      const starPath = d3.geoPath(projection).pointRadius(function (d) {
        if (starIDs.includes(d.id)) return 5;
        else return magnitudeScale(d.properties.mag);
      });

      const constellationLinesPath = d3.geoPath(projection);

      const tooltip_stars = d3
        .select(id)
        .append("div")
        .style("opacity", 0)
        .attr("class", "tooltip")
        .style("color", "white")
        .style("font-size", "28px")
        .style("background-color", "#113")
        .style("padding", "5px")
        .style("position", "absolute")
        .style("cursor", "pointer");

      const tooltip_constellation = d3
        .select(id)
        .append("div")
        .style("opacity", 0)
        .attr("class", "tooltip")
        .style("color", "white")
        .style("font-size", "28px")
        .style("background-color", "#113")
        .style("padding", "5px")
        .style("position", "absolute")
        .style("cursor", "pointer");

      const starFuture = d3
        .select(id)
        .append("div")
        .style("opacity", 0)
        .attr("class", "tooltip")
        .style("color", "white")
        .style("font-size", "28px")
        .style("background-color", "#113")
        .style("border", "solid")
        .style("border-width", "0.1px")
        .style("border-radius", "5px")
        .style("padding", "10px")
        .style("position", "absolute");

      // Tooltip
      const mouseover_stars = function (event, d) {
        tooltip_stars.style("opacity", 1);
      };

      const mouseover_constellation = function (event, d) {
        tooltip_constellation.style("opacity", 1);
      };

      const mousemove_stars = function (event, d) {
        if (starNames[d.id].name) {
          tooltip_stars
            .html(`Name: ${starNames[d.id].name}`)
            .style("left", 1000 + "px")
            .style("top", 1000 + "px");
        }
      };
      const mousemove_constellation = function (event, d) {
        for (let i = 0; i < 89; i++) {
          if (constellations.features[i].id == d.id) {
            tooltip_constellation
              .html(
                `Constellation: ${constellations.features[i].properties.name}`
              )
              .style("left", 1000 + "px")
              .style("top", 1100 + "px");
          }
        }
      };
      const mouseleave_stars = function (event, d) {
        tooltip_stars.transition().duration(200).style("opacity", 0);
      };
      const mouseleave_constellation = function (event, d) {
        tooltip_constellation.transition().duration(200).style("opacity", 0);
      };

      svg
        .append("g")
        .attr("id", "constellation")
        .selectAll("path")
        .data(constellationLines.features)
        .enter()
        .append("path")
        .attr("d", constellationLinesPath)
        .attr("fill", "#113")
        .attr("stroke", "#ddd")
        .attr("stroke-width", "0.5px")
        .on("mouseover", mouseover_constellation)
        .on("mousemove", mousemove_constellation)
        .on("mouseleave", mouseleave_constellation);

      // svg
      //   .selectAll("text")
      //   .data(constellationLines.features)
      //   .enter()
      //   .append("text")
      //   .text(function (d) {
      //     return d.id;
      //   })
      //   .attr("font_family", "sans-serif") // Font type
      //   .attr("font-size", "11px") // Font size
      // .attr("fill", "darkgreen");
      svg
        .append("g")
        .attr("id", "star")
        .selectAll("path")
        .data(stars.features)
        .enter()
        .append("path")
        .attr("d", starPath)
        .attr("fill", function (d) {
          if (d.id == 32349) return "rgb(204,121,167)";
          if (d.id == 27989) return "rgb(0,114,178)";
          if (d.id == 24436) return "rgb(0,114,178)";
          if (d.id == 30438) return "rgb(204,121,167)";
          if (d.id == 69673) return "rgb(0,158,115)";
          if (d.id == 80763) return "rgb(0,114,178)";
          if (d.id == 24608) return "rgb(0,158,115)";
          if (d.id == 102098) return "rgb(0,114,178)";
          if (d.id == 21421) return "rgb(0,158,115)";
          if (d.id == 97649) return "rgb(204,121,167)";
          if (d.id == 37826) return "rgb(0,158,115)";
          if (d.id == 36850) return "rgb(204,121,167)";
          if (d.id == 25336) return "rgb(204,121,167";
          if (d.id == 37279) return "rgb(204,121,167)";
          if (d.id == 91262) return "rgb(255,255,255)";
          if (d.id == 11767) return "rgb(255,255,255)";
          if (d.id == 68191) return "rgb(213,94,0)";
          else return "#fff";
        })
        .on("mouseover", mouseover_stars)
        .on("mousemove", mousemove_stars)
        .on("mouseleave", mouseleave_stars)
        .on("click", function (event, d) {
          if (starlist.includes(starNames[d.id].name)) {
            // console.log(d);
            starFuture.style("opacity", 1);
            starFuture
              .html(`Name: ${starNames[d.id.name]}`)
              .html(`Current Age: ${starObj[starNames[d.id].name].age}`)
              .html(`Magnitude: ${d.properties.mag}`)
              .html(`B-V Color Index: ${d.properties.bv}`)
              .html(`Current Stage: ${starObj[starNames[d.id].name].current}`)
              .html(`Predicted Future: ${starObj[starNames[d.id].name].future}`)
              .style("left", 900 + "px")
              .style("top", 900 + "px");
          } else starFuture.style("opacity", 0);
        });

      svg
        .call(
          d3.drag().on("drag", function (event) {
            var rotate = projection.rotate();
            var k = sensitivity / projection.scale();
            projection.rotate([
              rotate[0] + event.dx * k,
              rotate[1] - event.dy * k,
            ]);
            svg.selectAll("path").attr("d", constellationLinesPath);
            svg.selectAll("path").attr("d", starPath);
          })
        )
        .call(
          d3.zoom().on("zoom", (event) => {
            if (event.transform.k > 0.3) {
              projection.scale(initialScale * event.transform.k);
              // path = d3.geoPath().projection(projection);
              svg.selectAll("path").attr("d", starPath);
              svg.attr("r", projection.scale());
            } else {
              event.transform.k = 0.3;
            }
          })
        );
    },
  },
};
</script>

<style>
</style>