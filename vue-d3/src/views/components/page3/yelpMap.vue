<template>
    <div>
        <div id="mapid" style="height: 42vh;"></div>
    </div>
</template>
  
<script>
import * as L from 'leaflet';
import * as d3 from 'd3';
import 'leaflet/dist/leaflet.css';
import 'd3-tip';
import 'leaflet.heat/dist/leaflet-heat.js'
//import RestaurantRect from './restaurantRect.vue';


export default {
    name: "YelpMap",
    data() {
        return {
            map: null,
            myColor: null
        };
    },
    mounted() {
        // mapid is the id of the div where the map will appear
        const map = L
            .map("mapid")
            .setView([39, -95], 3); // center position + zoom
        // Add a tile to the map = a background. Comes from OpenStreetmap
        L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
            attribution: "Map data &copy; <a href=\"https://www.openstreetmap.org/\">OpenStreetMap</a>",
            maxZoom: 20,
        }).addTo(map);
        // Add a svg layer to the map
        L.svg().addTo(map);
        this.map = map;
        //var svgLayer = L.svg().addTo(map);
        //var svgGroup = d3.select(svgLayer._container)

        //L.marker([39, -95]).addTo(map)
        //var linear = d3.scaleLinear().domain([-1, 1]).range([0, 1]);
        var svg = d3.select("#mapid")
            .select("svg");
        const myColor = d3.scaleSequential()
            .interpolator(d3.interpolatePuOr)
            .domain([-1, 1]);
        d3.csv("data/restaurant.csv").then(function (data) {
            svg.selectAll("myCircles")
                .data(data)
                .enter()
                .append("circle")
                .attr("cx", d => map.latLngToLayerPoint([d.latitude, d.longitude]).x)
                .attr("cy", d => map.latLngToLayerPoint([d.latitude, d.longitude]).y)
                .attr("r", 5)
                // .style("fill", function (d, i) {
                //   //return d3.interpolateRgb("red", "green")(linear(d.sentiment_score));
                //   if (d.sentiment_score > 0) return "green"
                //   else return "red"
                // })
                // .attr("stroke", "red")
                .attr("fill", function (d) { return myColor(d.sentiment_score); })
                .attr("stroke-width", 1)
                .attr("stroke", function (d) { return myColor(d.sentiment_score); })
                .attr("fill-opacity", 0.5)
                .on("click", function (d) {
                    console.log("Clicked on circle:", d)
                    // // create a new popup and bind data about the clicked restaurant
                    // var popup = L.popup()
                    //     .setLatLng([d.latitude, d.longitude])
                    //     .setContent("<b>" + d.name + "</b><br>" +
                    //         "Rating: " + d.rating + "<br>" +
                    //         "Sentiment Score: " + d.sentiment_score.toFixed(2));

                    // // open the popup on the map
                    // popup.openOn(map);
                })


            // Function that update circle position if something change
            function update() {
                svg.selectAll("circle")
                    .attr("cx", d => map.latLngToLayerPoint([d.latitude, d.longitude]).x)
                    .attr("cy", d => map.latLngToLayerPoint([d.latitude, d.longitude]).y)
            }
            // If the user change the map (zoom or drag), I update circle position:
            map.on("moveend", update);
        });
        const data = [[0, 0.1]];
        const heatmapLayer = L.heatLayer(data, {
            radius: 50,
            gradient: {
                0: myColor(-1),
                0.1: myColor(-0.8),
                0.2: myColor(-0.6),
                0.3: myColor(-0.4),
                0.4: myColor(-0.2),
                0.5: myColor(0),
                0.6: myColor(0.2),
                0.7: myColor(0.4),
                0.8: myColor(0.6),
                0.9: myColor(0.8),
            },
        }).addTo(map);
        // add the heatmap legend control
        const legend = L.control({ position: "bottomleft" });
        legend.onAdd = function () {
            const div = L.DomUtil.create("div", "heatmap-legend");
            const gradient = heatmapLayer.options.gradient;
            const span = L.DomUtil.create("span", "heatmap-legend__item", div);
            span.style.backgroundColor = null;
            span.textContent = -1;
            for (const key in gradient) {
                const color = gradient[key];
                // const label = key
                const span = L.DomUtil.create("span", "heatmap-legend__item", div);
                span.style.backgroundColor = color;
                // span.textContent = label
            }
            const span1 = L.DomUtil.create("span", "heatmap-legend__item", div);
            span1.style.backgroundColor = null;
            span1.textContent = 1;
            const span2 = L.DomUtil.create("span", "heatmap-legend__item", div);
            span2.style.backgroundColor = null;
            span2.textContent = "sentiment";
            const span3 = L.DomUtil.create("span", "heatmap-legend__item", div);
            span3.style.backgroundColor = null;
            span3.textContent = "score";
            return div;
        };
        legend.addTo(map);
    },
    methods: {
        updateMap(name, sentiment_score) {
            const myColor = d3.scaleSequential()
                .interpolator(d3.interpolatePuOr)
                .domain([-1, 1]);
            //state is same to the state name in the map, then highlight the state
            d3.select("#mapid").selectAll("circle")
                .attr("fill", function (d) {
                    if (d.name == name && d.sentiment_score == sentiment_score) {
                        return "yellow";
                    }
                    else {
                        return myColor(d.sentiment_score);
                    }
                })
                .attr("r", function (d) {
                    if (d.name == name && d.sentiment_score == sentiment_score) {
                        return 20;
                    }
                    else {
                        return 5;
                    }
                });


            console.log("update map");
            console.log(name);
        },
        updateMapState(state) {
            console.log(this);
            if (state == "AB") {
                this.map.setView([53.5, -113.5], 9)
            }
            if (state == "AZ") {
                this.map.setView([32.2,-110.9], 9)
            }
            if (state == "CA") {
                this.map.setView([34.4, -119.6], 9)
            }
            if (state == "CO") {
                this.map.setView([32.2,-110.9], 9)
            }
            if (state == "DE") {
                this.map.setView([39.7, -75.6], 9)
            }
            if (state == "FL") {
                this.map.setView([28, -82.5], 9)
            }
            if (state == "HI") {
                this.map.setView([38.6, -90.3], 9)
            }
            if (state == "ID") {
                this.map.setView([39, -95], 9)
            }
            if (state == "IL") {
                this.map.setView([43.6, -116.3], 9)
            }
            if (state == "IN") {
                this.map.setView([39.9, -86.1], 9)
            }
            if (state == "LA") {
                this.map.setView([29.9, -90], 9)
            }
            if (state == "MO") {
                this.map.setView([38.6, -90.3], 9)
            }
            if (state == "MT") {
                this.map.setView([27.7, -82.6], 9)
            }
            if (state == "NC") {
                this.map.setView([36.1, -86.7], 9)
            }
            if (state == "NJ") {
                this.map.setView([39.8, -75.0], 9)
            }
            if (state == "NV") {
                this.map.setView([39.5, -119.8], 9)
            }
            if (state == "PA") {
                this.map.setView([40, -75.5], 9)
            }
            if (state == "TN") {
                this.map.setView([36.1, -86.7], 9)
            }
            if (state == "XMS") {
                this.map.setView([38.6, -90.3], 9)
            }
            d3.select("#mapid").selectAll("circle")
                .attr("opacity", function (d) {
                    if (d.state == state) {
                        return 1;
                    }
                    else {
                        return 0;
                    }
                })
        },
        updateMapState1(state) {
            console.log(this);
            //this.map.setView([39, -95], 3)
            d3.select("#mapid").selectAll("circle")
                .attr("opacity", function (d) {
                    if (d.state == state) {
                        return 1;
                    }
                    else {
                        return 1;
                    }
                })
        }

    }
}
</script>
<style>
.heatmap-legend {
    background-color: null;
    padding: 5px;
    border: 1px solid null;
}

.heatmap-legend__item {
    display: block;
    margin-left: 1px;
    width: 20px;
    height: 20px;
}
</style>