<template>
  <div>
    <div id="chartId"></div>
    <!-- <input type="range" name="mySlider" id="sliderId" min="1" max="3000" value="3000"> -->
    <div ref="slider"></div>
    <div id="legendId"></div>

  </div>
</template>
  
<script>
//import * as L from 'leaflet';
import * as d3 from 'd3';
import 'leaflet/dist/leaflet.css';
//import restaurantRect from './restaurantRect.vue';
//import { schemeCategory20 as d3Category20 } from 'd3-scale-chromatic';
// import 'd3-tip';


export default {
  name: 'MyChart',
  mounted() {
    // set the dimensions and margins of the graph
    const margin = { top: 20, right: 30, bottom: 20, left: 50 },
      width =window.innerWidth*0.26 - margin.left - margin.right,
      height = window.innerHeight*0.40 - margin.top - margin.bottom;

    // append the svg object to the body of the page
    const svg = d3.select('#chartId')
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform",
        `translate(${margin.left}, ${margin.top})`);

    //Read the data
    d3.csv('data/line.csv').then(data => {
      // Add X axis
      const x = d3.scaleLinear()
        .domain([-0.5, 0.8])
        .range([0, width]);
      svg.append("g")
        .attr("transform", `translate(0, ${height})`)
        .call(d3.axisBottom(x));
      svg.append("text")
        .attr("text-anchor", "end")
        .attr("x", width)
        .attr("y", height + 40)
        .text("sentiment score");


      // Add Y axis
      const y = d3.scaleLinear()
        .domain([0, 3000])
        .range([height, 0]);
      const yAxis = svg.append("g")
        .attr("transform", `translate(0,0)`)
        .call(d3.axisLeft(y));
      svg.append("text")
        .attr("text-anchor", "end")
        .attr("x", 40)
        .attr("y", -10)
        .text("# of restaurants");

      const states = data.columns.slice(1, 20);
      // color palette
      // const color = d3.scaleOrdinal()
      //   .domain(states)
      //   .range(['#e41a1c', '#377eb8', '#4daf4a', '#984ea3', '#ff7f00', '#ffff33', '#a65628', '#f781bf', '#999999', '#9966ff',
      //     '#66ff99', '#ffcc66', '#cc3399', '#669900', '#ff66ff', '#339933', '#3399ff', '#990033', '#000099']);
      const color = d3.scaleOrdinal()
        .domain(states)
        .range(["#3366cc", "#dc3912", "#ff9900", "#109618", "#990099", "#0099c6", "#dd4477", "#66aa00", "#b82e2e", "#316395", "#994499", "#22aa99", "#aaaa11", "#6633cc", "#e67300", "#8b0707", "#651067", "#329262", "#5574a6", "#3b3eac"])
      const getState = d3.scaleOrdinal()
        .domain(["#3366cc", "#dc3912", "#ff9900", "#109618", "#990099", "#0099c6", "#dd4477", "#66aa00", "#b82e2e", "#316395", "#994499", "#22aa99", "#aaaa11", "#6633cc", "#e67300", "#8b0707", "#651067", "#329262", "#5574a6", "#3b3eac"])
        .range(states);

      const div = d3.select('#chartId').append("div")
        .attr("class", "tooltip")
        .style("opacity", 0);

      function draw(state) {
        svg
          .append("path")
          .datum(data)
          .attr("fill", "none")
          .attr("stroke", function () { return color(state) })
          .attr("stroke-width", 1)
          .attr("d", d3.line()
            .curve(d3.curveBasis)
            .x(function (d) { return x(d.score) })
            .y(function (d) {
              return y(d[state])
            }))
          .on("mouseover", function (event) {
            div.transition()
              .duration(200)
              .style("opacity", 0.9);
            div.html(state)
              .style("width", "50px")
              .style("height", "18px")
              .style("left", (event.pageX - 100) + "px")
              .style("top", (event.pageY - 128) + "px");
            d3.select(this)
              .style("stroke-width", 3)
              .style("opacity", 1)
          })
          .on("mouseout", function () {
            div.transition()
              .duration(500)
              .style("opacity", 0);
          })
          .on("mouseleave", function () {
            d3.select(this)
              .style("stroke-width", 1)
              .style("stroke", function () { return color(state) })
              .style("opacity", 1)
          })

      }

      for (var i in states) {
        draw(states[i]);
        var size = 8
        svg.selectAll("myline")
          .data(states)
          .enter()
          .append("line")
          .attr("x1", 260)
          .attr("y1", function (d, i) { return 5 + i * (size + 5) }) // 100 is where the first dot appears. 25 is the distance between dots
          .attr("x2", 270)
          .attr("y2", function (d, i) { return 5 + i * (size + 5) })
          .attr("width", size)
          .attr("height", size)
          .style("stroke", function (d) { return color(d) })
          .attr("stroke-width", "2px");


        svg.selectAll("mylabels")
          .data(states)
          .enter()
          .append("text")
          .attr("x", 270 + size * 1.2)
          .attr("y", function (d, i) { return 5 + i * (size + 5) }) // 100 is where the first dot appears. 25 is the distance between dots
          .style("fill", function (d) { return color(d) })
          .text(function (d) { return d })
          .attr("text-anchor", "left")
          .style("alignment-baseline", "middle")
      }



      var value;
      // // Update chart
      function updatePlot() {
        svg.selectAll("path").remove()
        // Get the value of the button


        // Update Y axis
        y.domain([0, value])
        yAxis.transition().duration(1000).call(d3.axisLeft(y))
        svg.append("g")
          .attr("transform", `translate(0, ${height})`)
          .call(d3.axisBottom(x));

        for (var i in states) {
          draw(states[i])

        }

      }



      //  d3.select("#mySlider").on("change", updatePlot)

      // Set up the slider

      d3.select('#chartId')
        .append('input')
        .attr('type', 'range')
        .attr('min', 1)
        .attr('max', 3000)
        .attr('step', 10)
        .attr('value', 3000)
        .style('position', 'relative') // enable positioning
        .style('left', '20px') // move slider 100 pixels to the right
        .on('input', (event) => {
          this.sliderValue = +event.target.value; // update the slider value
          value = this.sliderValue
          updatePlot();

          svg.selectAll("path")
            .on("mouseover", function (event) {
              var state = getState(event.target.attributes[1].value)
              div.transition()
                .duration(200)
                .style("opacity", 0.9);
              div.html(state)
                .style("width", "50px")
                .style("height", "18px")
                .style("left", (event.pageX - 100) + "px")
                .style("top", (event.pageY - 128) + "px");
              d3.select(event.target)
                .style("stroke-width", 3)
                .style("opacity", 1)
              this.$emit('click-line', state)
              this.$emit('click-line1', state)
            }.bind(this))
            .on("mouseout", function () {
              div.transition()
                .duration(500)
                .style("opacity", 0);
            })
            .on("mouseleave", function () {
              var state = getState(event.target.attributes[1].value)
              d3.select(event.target)
                .style("stroke-width", 1)
                .style("stroke", function () { return color(state) })
                .style("opacity", 1)
              this.$emit('unclick-line', state)
              this.$emit('unclick-line1', state)
            }.bind(this))
        });

      // select all the paths in the svg and send to the parent component
      svg.selectAll("path")
        .on("mouseover", function (event) {
          var state = getState(event.target.attributes[1].value)
          div.transition()
            .duration(200)
            .style("opacity", 0.9);
          div.html(state)
            .style("width", "50px")
            .style("height", "18px")
            .style("left", (event.pageX - 100) + "px")
            .style("top", (event.pageY - 128) + "px");
          d3.select(event.target)
            .style("stroke-width", 3)
            .style("opacity", 1)
          this.$emit('click-line', state)
          this.$emit('click-line1', state)
        }.bind(this))
        .on("mouseout", function () {
          div.transition()
            .duration(500)
            .style("opacity", 0);
        })
        .on("mouseleave", function () {
          var state = getState(event.target.attributes[1].value)
          d3.select(event.target)
            .style("stroke-width", 1)
            .style("stroke", function () { return color(state) })
            .style("opacity", 1)
          this.$emit('unclick-line', state)
          this.$emit('unclick-line1', state)
        }.bind(this))


      // svg.select('#chartId').selectAll("path")
      //   .on("click", function (event) {
      //     console.log(event.target)
      //     var state = getState(event.target.attributes[1].value)
      //     this.$emit('click-line', state)
      //   }.bind(this))

    })

    const svg1 = d3.select('#chartId')
      .append("svg")
      .attr("width", 400)
      .attr("height", 15)
      .append("g")
    svg1
      .append('text')
      .attr('y', 12)
      .attr('x', 20)
      .text("1");
    svg1
      .append('text')
      .attr('y', 12)
      .attr('x', 130)
      .text("3000");


  }
}
</script>
  
<style>
div.tooltip {
  position: absolute;
  text-align: center;
  width: 60px;
  height: 18px;
  padding: 2px;
  font: 12px sans-serif;
  background: white;
  border: 0px;
  border-color: black;
  border-radius: 8px;
  pointer-events: none;
}
</style>
  
  
  
  
  