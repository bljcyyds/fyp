<template>
  <div>
    <div ref="heatmap"></div>
  </div>
</template>

<script>
import * as d3 from 'd3';

export default {
  name: 'HeatMap',
  mounted() {
    this.loadData();
  },
  methods: {
    async loadData() {
      const data = await d3.csv('data/heatmap.csv');
      this.drawChart(data);
    },
    drawChart(data) {
      const margin = { top: 20, right: 30, bottom: 70, left: 90 };
      const width = window.innerWidth*0.26 - margin.left - margin.right;
      const height = window.innerHeight*0.42 - margin.top - margin.bottom;

      const svg = d3.select(this.$refs.heatmap)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", `translate(${margin.left}, ${margin.top})`);

      const x = d3.scaleLinear()
        .domain([0, 13000])
        .range([0, width]);

      svg.append("g")
        .attr("transform", `translate(0, ${height})`)
        .call(d3.axisBottom(x))
        .selectAll("text")
        .attr("transform", "translate(-10,0)rotate(-45)")
        .style("text-anchor", "end");

      svg.append("text")
        .attr("text-anchor", "end")
        .attr("x", width)
        .attr("y", height + 50)
        .text("# of restaurants");

      const y = d3.scaleBand()
        .range([0, height])
        .domain(data.map(d => d.state))
        .padding(.1);

      svg.append("g")
        .call(d3.axisLeft(y))


      svg.append("text")
        .attr("text-anchor", "end")
        .attr("x", 40)
        .attr("y", -10)
        .text("state name");

      const myColor = d3.scaleSequential()
        .interpolator(d3.interpolatePuOr)
        .domain([-1, 1])

      const div = d3.select("body").append("div")
        .attr("class", "tooltip")
        .style("opacity", 0);

      svg.selectAll("myRect")
        .data(data)
        .join("rect")
        .attr("x", d => x((d.index) * 1))
        .attr("y", d => y(d.state))
        .attr("width", 1)
        .attr("height", 10)
        .attr("fill", function (d) { return myColor(d.sentiment_score) })
        .on("mouseover", function (event, d) {
          div.transition()
            .duration(200)
            .style("opacity", .9);
          div.html("restaurant name: " + d.name + ",\n sentiment score: " + d.sentiment_score)
            .style("left", (event.pageX + 10) + "px")
            .style("top", (event.pageY - 28) + "px");
          d3.select(this)
            .style("stroke", "black")
            .style("opacity", 1)        
        })
        .on("mouseout", function () {
          div.transition()
            .duration(500)
            .style("opacity", 0);
        })
        .on("mouseleave", function () {
          div
            .style("opacity", 0)
          d3.select(this)
            .style("stroke", "none")
            .style("opacity", 0)
        })
        .on("click",function(event, d){
          console.log(d.name)
          this.$emit('click-rect', d.name,d.sentiment_score) 
          //console.log(this)
        }.bind(this))

    },
    updateRectColor(state){
      //console.log(state)
      d3.selectAll("rect")
        .attr("opacity", function (d) { 
          if(d.state == state){
            return 1
          }
          else{
            return 0
          }
        })
    },
    updateRectColor1(state){
      //console.log(state)
      d3.selectAll("rect")
        .attr("opacity", function (d) { 
          if(d.state == state){
            return 1
          }
          else{
            return 1
          }
        })
    }
  }
}
</script>   
<style>
div.tooltip {
  position: absolute;
  text-align: center;
  width: 200px;
  height: 50px;
  padding: 2px;
  font: 12px sans-serif;
  background: white;
  border: 0px;
  border-color: black;
  border-radius: 8px;
  pointer-events: none;
}

.d3.axisBottom {
  border-color: black;
}
</style>