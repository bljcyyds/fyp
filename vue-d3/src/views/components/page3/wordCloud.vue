<template>
    <div ref="wordCloud"></div>
</template>
  
<script>
import * as d3 from 'd3';
import * as cloud from 'd3-cloud';

export default {
    data() {
        return {
            data: 'wordCloud',
        }
    },
    mounted() {
        d3.select(this.$refs.wordCloud).selectAll("*").remove();
        d3.csv('data/wordCloud/wordCloud.csv').then(data => {
            const fill = d3.scaleOrdinal(d3.schemeCategory10);
            const width = window.innerWidth*0.26;
            const height = window.innerHeight*0.43;

            const layout = cloud()
                .size([width, height])
                .words(data)
                .padding(5)
                .rotate(() => ~~(Math.random() * 2) * 90)
                .font('Impact')
                .fontSize(d => d.count / 100000 * 2)
                .on('end', () => {
                    const svg = d3.select(this.$refs.wordCloud).append('svg')
                        .attr('width', width)
                        .attr('height', height);

                    const wordG = svg.append('g')
                        .attr('transform', `translate(180, 200)`);

                    wordG.selectAll('text')
                        .data(layout.words())
                        .enter().append('text')
                        .style('font-size', d => `${d.count / 100000 * 2}px`)
                        .style('font-family', 'Impact')
                        .style("fill", (d, i) => fill(i))
                        .attr('text-anchor', 'middle')
                        .attr('transform', d => `translate(${[d.x, d.y]})rotate(${d.rotate})`)
                        .text(d => d.text)
                })


            layout.start();
        });
    },
    methods: {
        clickNetwork(name) {
            console.log(name)
            d3.select(this.$refs.wordCloud).selectAll("*").remove();
            d3.csv('data/wordCloud/wordCloud.csv').then(data => {
                const fill = d3.scaleOrdinal(d3.schemeCategory10);
                const width = window.innerWidth*0.26;
                const height = window.innerHeight*0.43;
                const layout = cloud()
                    .size([width, height])
                    .words(data)
                    .padding(5)
                    .rotate(() => ~~(Math.random() * 2) * 90)
                    .font('Impact')
                    .fontSize(20 )
                    .on('end', () => {
                        //console.log(d => d.count)
                        const svg = d3.select(this.$refs.wordCloud).append('svg')
                            .attr('width', width)
                            .attr('height', height);
                        const wordG = svg.append('g')
                            .attr('transform', `translate(180, 200)`);
                        wordG.selectAll('text')
                            .data(layout.words())
                            .enter().append('text')
                            .style('font-size', `20px`)
                            .style('font-family', 'Impact')
                            .style("fill", (d, i) => fill(i))
                            .attr('text-anchor', 'middle')
                            .attr('transform', d => `translate(${[d.x, d.y]})rotate(${d.rotate})`)
                            .text(d => d.text)
                    })
                layout.start();



            });
        },
        clickNetwork1(name) {
            console.log(name)
            d3.select(this.$refs.wordCloud).selectAll("*").remove();
            d3.csv('data/wordCloud/wordCloud.csv').then(data => {
                const fill = d3.scaleOrdinal(d3.schemeCategory10);
                const width = window.innerWidth*0.26;
                const height = window.innerHeight*0.43;

                const layout = cloud()
                    .size([width, height])
                    .words(data)
                    .padding(5)
                    .rotate(() => ~~(Math.random() * 2) * 90)
                    .font('Impact')
                    .fontSize(d => d.count / 100000 * 2)
                    .on('end', () => {
                        const svg = d3.select(this.$refs.wordCloud).append('svg')
                            .attr('width', width)
                            .attr('height', height);

                        const wordG = svg.append('g')
                            .attr('transform', `translate(180, 200)`);

                        wordG.selectAll('text')
                            .data(layout.words())
                            .enter().append('text')
                            .style('font-size', d => `${d.count / 100000 * 2}px`)
                            .style('font-family', 'Impact')
                            .style("fill", (d, i) => fill(i))
                            .attr('text-anchor', 'middle')
                            .attr('transform', d => `translate(${[d.x, d.y]})rotate(${d.rotate})`)
                            .text(d => d.text)
                    })


                layout.start();
            });
        },

    }
}
</script>
  
  