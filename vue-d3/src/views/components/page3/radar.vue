<template>
    <div ref="radarChart"></div>
</template>
  
<script>
import * as d3 from 'd3';

export default {
    mounted() {
        this.drawChart();
    },
    methods: {
        drawChart() {
            d3.select(this.$refs.radarChart).selectAll("*").remove();
            // Define the data for the chart
            const sampledata = [
                { name: 'compliment_hot', value: 5000 },
                { name: 'compliment_more', value: 5000 },
                { name: 'compliment_profile', value: 5000 },
                { name: 'compliment_cute', value: 5000 },
                { name: 'compliment_list', value: 5000 },
                { name: 'compliment_note', value: 5000 },
                { name: 'compliment_plain', value: 5000 },
                { name: 'compliment_cool', value: 5000 },
                { name: 'compliment_funny', value: 5000 },
                { name: 'compliment_writer', value: 5000 },
            ];
            const sampledata1 = [
                { name: 'compliment_hot', value: 3000 },
                { name: 'compliment_more', value: 3000 },
                { name: 'compliment_profile', value: 3000 },
                { name: 'compliment_cute', value: 3000 },
                { name: 'compliment_list', value: 3000 },
                { name: 'compliment_note', value: 3000 },
                { name: 'compliment_plain', value: 3000 },
                { name: 'compliment_cool', value: 3000 },
                { name: 'compliment_funny', value: 3000 },
                { name: 'compliment_writer', value: 3000 },
            ];

            // Set the dimensions of the chart
            const width = window.innerWidth * 0.26;
            const height = window.innerHeight * 0.43;

            // Set the center point for the chart
            const centerX = width / 2;
            const centerY = height / 2;

            // Set the maximum value for the chart
            const maxValue = 5000;

            // Define the scale for the chart
            const scale = d3.scaleLinear()
                .domain([0, maxValue])
                .range([0, centerY - 50]);

            d3.json('data/radar/medium.json').then(data => {

                // Define the line function
                const line = d3.lineRadial()
                    .angle((d, i) => i * (Math.PI * 2) / data.length)
                    .radius(d => scale(d.value))
                    .curve(d3.curveLinearClosed);

                // Create the SVG element and set its dimensions
                const svg = d3.select(this.$refs.radarChart)
                    .append('svg')
                    .attr('width', width)
                    .attr('height', height);

                // Draw the chart
                const radarChart = svg.append('g')
                    .attr('transform', `translate(${centerX}, ${centerY})`);

                // Draw the axis lines
                const axisLines = radarChart.selectAll('.axis')
                    .data(data)
                    .enter()
                    .append('g')
                    .attr('class', 'axis');

                axisLines.append('line')
                    .attr('x1', 0)
                    .attr('y1', 0)
                    .attr('x2', (d, i) => scale(maxValue) * Math.sin(i * (Math.PI * 2) / data.length))
                    .attr('y2', (d, i) => scale(maxValue) * Math.cos(i * (Math.PI * 2) / data.length))
                    .attr('stroke', 'grey')
                    .attr('stroke-width', 1);

                // Draw the axis labels
                axisLines.append('text')
                    .attr('x', (d, i) => scale(maxValue * 1.2) * Math.sin(i * (Math.PI * 2) / data.length))
                    .attr('y', (d, i) => scale(maxValue * 1.2) * Math.cos(i * (Math.PI * 2) / data.length))
                    .attr('text-anchor', 'middle')
                    .attr('alignment-baseline', 'middle')
                    .text(d => d.name)
                    .attr('fill', 'grey');

                // Draw the data points
                const dataPoints = radarChart.append('g')
                    .attr('class', 'data-points');


                // Draw the data lines
                dataPoints.append('path')
                    .datum(data)
                    .attr('d', line)
                    .attr('fill', 'none')
                    .attr('stroke', 'blue')
                    .attr('stroke-width', 2);
                dataPoints.append('path')
                    .datum(sampledata)
                    .attr('d', line)
                    .attr('fill', 'none')
                    .attr('stroke', 'grey')
                    .attr('stroke-width', 1);
                dataPoints.append('path')
                    .datum(sampledata1)
                    .attr('d', line)
                    .attr('fill', 'none')
                    .attr('stroke', 'grey')
                    .attr('stroke-width', 1);
            });
        },
        updateRadar(name) {
            d3.select(this.$refs.radarChart).selectAll("*").remove();
            // Define the data for the chart
            const sampledata = [
                { name: 'compliment_hot', value: 5000 },
                { name: 'compliment_more', value: 5000 },
                { name: 'compliment_profile', value: 5000 },
                { name: 'compliment_cute', value: 5000 },
                { name: 'compliment_list', value: 5000 },
                { name: 'compliment_note', value: 5000 },
                { name: 'compliment_plain', value: 5000 },
                { name: 'compliment_cool', value: 5000 },
                { name: 'compliment_funny', value: 5000 },
                { name: 'compliment_writer', value: 5000 },
            ];
            const sampledata1 = [
                { name: 'compliment_hot', value: 3000 },
                { name: 'compliment_more', value: 3000 },
                { name: 'compliment_profile', value: 3000 },
                { name: 'compliment_cute', value: 3000 },
                { name: 'compliment_list', value: 3000 },
                { name: 'compliment_note', value: 3000 },
                { name: 'compliment_plain', value: 3000 },
                { name: 'compliment_cool', value: 3000 },
                { name: 'compliment_funny', value: 3000 },
                { name: 'compliment_writer', value: 3000 },
            ];

            // Set the dimensions of the chart
            const width = window.innerWidth * 0.26;
            const height = window.innerHeight * 0.43;

            // Set the center point for the chart
            const centerX = width / 2;
            const centerY = height / 2;

            // Set the maximum value for the chart
            const maxValue = 5000;

            // Define the scale for the chart
            const scale = d3.scaleLinear()
                .domain([0, maxValue])
                .range([0, centerY - 50]);

            d3.json('data/radar/' + name + '.json').then(data => {
                // Define the line function
                const line = d3.lineRadial()
                    .angle((d, i) => i * (Math.PI * 2) / data.length)
                    .radius(d => scale(d.value))
                    .curve(d3.curveLinearClosed);

                // Create the SVG element and set its dimensions
                const svg = d3.select(this.$refs.radarChart)
                    .append('svg')
                    .attr('width', width)
                    .attr('height', height);

                // Draw the chart
                const radarChart = svg.append('g')
                    .attr('transform', `translate(${centerX}, ${centerY})`);

                // Draw the axis lines
                const axisLines = radarChart.selectAll('.axis')
                    .data(data)
                    .enter()
                    .append('g')
                    .attr('class', 'axis');

                axisLines.append('line')
                    .attr('x1', 0)
                    .attr('y1', 0)
                    .attr('x2', (d, i) => scale(maxValue) * Math.sin(i * (Math.PI * 2) / data.length))
                    .attr('y2', (d, i) => scale(maxValue) * Math.cos(i * (Math.PI * 2) / data.length))
                    .attr('stroke', 'grey')
                    .attr('stroke-width', 1);

                // Draw the axis labels
                axisLines.append('text')
                    .attr('x', (d, i) => scale(maxValue * 1.2) * Math.sin(i * (Math.PI * 2) / data.length))
                    .attr('y', (d, i) => scale(maxValue * 1.2) * Math.cos(i * (Math.PI * 2) / data.length))
                    .attr('text-anchor', 'middle')
                    .attr('alignment-baseline', 'middle')
                    .text(d => d.name)
                    .attr('fill', 'grey');

                // Draw the data points
                const dataPoints = radarChart.append('g')
                    .attr('class', 'data-points');


                // Draw the data lines
                dataPoints.append('path')
                    .datum(data)
                    .attr('d', line)
                    .attr('fill', 'none')
                    .attr('stroke', 'blue')
                    .attr('stroke-width', 2);
                dataPoints.append('path')
                    .datum(sampledata)
                    .attr('d', line)
                    .attr('fill', 'none')
                    .attr('stroke', 'grey')
                    .attr('stroke-width', 1);
                dataPoints.append('path')
                    .datum(sampledata1)
                    .attr('d', line)
                    .attr('fill', 'none')
                    .attr('stroke', 'grey')
                    .attr('stroke-width', 1);
            });
        }
    }
}
</script>