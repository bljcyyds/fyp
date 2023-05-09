<template>
  <div>
    <svg ref="my_dataviz" style="height: 43vh;width: 100%"></svg>
  </div>
</template>
  
<script>
import * as d3 from 'd3';
//import { style } from 'd3';
import data from '/Users/bjxl/Downloads/vue-echarts-master1/public/data/node_2000.json';

export default {
  mounted() {
    //const margin = { top: 10, right: 30, bottom: 30, left: 40 },
    // width = 549 - margin.left - margin.right,
    // height = 389 - margin.top - margin.bottom;

    const svg1 = d3.select(this.$refs.my_dataviz)
      // .attr("width", width + margin.left + margin.right)
      // .attr("height", height + margin.top + margin.bottom)
      .append("g")
    // .attr("transform",
    //   `translate(${margin.left}, ${margin.top})`);

    const myColor = d3.scaleSequential().domain([0, 5])
      .interpolator(d3.interpolateViridis)

    const link = svg1.append("g")
      .attr("class", "links")
      .selectAll("line")
      .data(data.links)
      .enter().append("line")
      .attr("stroke-width", function (d) { return Math.sqrt(d.value); });

    const node = svg1.append("g")
      .attr("class", "nodes")
      .selectAll("circle")
      .data(data.nodes)
      .enter().append("circle")
      .attr("r", function (d) { return d.review_count * 0.002; })
      .attr("fill", function (d) { return myColor(d.average_stars); })
      .call(d3.drag()
        .on("start", dragstarted)
        .on("drag", dragged)
        .on("end", dragended))
    function dragstarted(event, d) {
      if (!event.active) simulation.alphaTarget(.03).restart();
      d.fx = d.x;
      d.fy = d.y;
    }
    function dragged(event, d) {
      d.fx = event.x;
      d.fy = event.y;
    }
    function dragended(event, d) {
      if (!event.active) simulation.alphaTarget(.03);
      d.fx = null;
      d.fy = null;
    }

    const simulation = d3.forceSimulation(data.nodes)
      .force("link", d3.forceLink()
        .id(function (d) { return d.id; })
        .links(data.links)
      )
      .force("charge", d3.forceManyBody().strength(-50))
      .force("center", d3.forceCenter(this.$refs.my_dataviz.clientWidth / 2, this.$refs.my_dataviz.clientHeight / 2))
      .on("tick", ticked);

    node.append("title")
      .text(function (d) { return d.name; });

    node.on("mouseover", function (d) {
      link.style('stroke-opacity', function (l) {
        if (d.target.__data__ == l.source) return 1;
        else return 0.05;
      })
      console.log(d.target.__data__.name)
      this.$emit('click-network', d.target.__data__.name)
      this.$emit('click-network2', d.target.__data__.name)
    }.bind(this))
      .on("mouseout",function (d) {
        link.style("stroke-opacity", 0.05)
        this.$emit('click-network1', d.target.__data__.name)
        this.$emit('click-network3', d.target.__data__.name)
      }.bind(this))

    function ticked() {
      link
        .attr("x1", function (d) { return d.source.x; })
        .attr("y1", function (d) { return d.source.y; })
        .attr("x2", function (d) { return d.target.x; })
        .attr("y2", function (d) { return d.target.y; });

      node
        .attr("cx", function (d) { return d.x; })
        .attr("cy", function (d) { return d.y; });
    }
    //legend
    var linearScale = d3.scaleLinear()
      .domain([0, 5])
      .range([0, 100]);

    var sequentialScale = d3.scaleSequential()
      .domain([0, 5]);

    var interpolators = [
      'interpolateViridis'
    ];
    var myData = d3.range(0, 5, 1);

    var height = this.$refs.my_dataviz.clientHeight - 15;
    function dots(d) {
      sequentialScale
        .interpolator(d3[d]);

      d3.select(this)
        .append('text')
        .attr('y', height)
        .attr('x', 0)
        .text("0");
      d3.select(this)
        .append('text')
        .attr('y', height)
        .attr('x', 15)
        .text("1");
      d3.select(this)
        .append('text')
        .attr('y', height)
        .attr('x', 35)
        .text("2");

      d3.select(this)
        .append('text')
        .attr('y', height)
        .attr('x', 55)
        .text("3");
      d3.select(this)
        .append('text')
        .attr('y', height)
        .attr('x', 75)
        .text("4");
      d3.select(this)
        .append('text')
        .attr('y', height)
        .attr('x', 95)
        .text("5");

      d3.select(this)
        .selectAll('rect')
        .data(myData)
        .enter()
        .append('rect')
        .attr('x', function (d) {
          return linearScale(d);
        })
        .attr('y', height + 5)
        .attr('width', 20)
        .attr('height', 10)
        .style('fill', function (d) {
          return sequentialScale(d);
        });
      d3.select(this)
        .append('text')
        .attr('y', height + 12)
        .attr('x', 110)
        .text("average stars");
      d3.select(this)
        .append('text')
        .attr('y', height - 15)
        .attr('x', 20)
        .text("review count");
    }

    svg1.selectAll('legend')
      //.selectAll('g.interpolator')
      .data(interpolators)
      .enter()
      .append('g')
      .classed('interpolator', true)
      .attr('transform', function (d, i) {
        return 'translate(0, ' + (i * 70) + ')';
      })
      .each(dots);

    var size = d3.scaleSqrt()
      .domain([1, 100])  // What's in the data, let's say it is percentage
      .range([1, 100])  // Size in pixel

    // Add legend: circles
    var valuesToShow = [2000 * 0.002, 5000 * 0.002, 10000 * 0.002, 17500 * 0.002]
    var xCircle = 60
    var xLabel = 150
    var yCircle = height - 25
    svg1
      .selectAll("legend")
      .data(valuesToShow)
      .enter()
      .append("circle")
      .attr("cx", xCircle)
      .attr("cy", function (d) { return yCircle - size(d) })
      .attr("r", function (d) { return size(d) })
      .style("fill", "none")
      .attr("stroke", "black")

    // Add legend: segments
    svg1
      .selectAll("legend")
      .data(valuesToShow)
      .enter()
      .append("line")
      .attr('x1', function (d) { return xCircle + size(d) })
      .attr('x2', xLabel)
      .attr('y1', function (d) { return yCircle - size(d) })
      .attr('y2', function (d) { return yCircle - size(d) })
      .attr('stroke', 'black')
      .style('stroke-dasharray', ('2,2'))

    // Add legend: labels
    svg1
      .selectAll("legend")
      .data(valuesToShow)
      .enter()
      .append("text")
      .attr('x', xLabel)
      .attr('y', function (d) { return yCircle - size(d) })
      .text(function (d) { return d / 0.002 })
      .style("font-size", 10)
      .attr('alignment-baseline', 'middle')

  }

}

</script>
<style>
.links line {
  stroke: #999;
  stroke-opacity: 0.1;
}

.nodes circle {
  stroke: #fff;
  stroke-width: 1.5px;
}
</style>