<template>
    <div ref="$wrapper">
        <!-- <svg width="500" height="500">
                <rect fill="blue" x="0" y="350" height="150" width="100"></rect>
                <rect fill="blue" x="100" y="450" height="50" width="100"></rect>
                <rect fill="blue" x="200" y="380" height="120" width="100"></rect>
                <rect fill="blue" x="300" y="40" height="460" width="100"></rect>
                <rect fill="blue" x="400" y="180" height="320" width="100"></rect>
        </svg>-->
        <!-- <svg :width="width" :height="height">
            <g transform="translate(50, 50)">
                <rect
                    v-for="(i, idx) in charts"
                    :key="i"
                    style="transition: all 1s ease 0.5s;"
                    fill="blue"
                    :x="idx * innerWidth / charts.length"
                    :y="innerHeight - i"
                    :height="i"
                    :width="innerWidth / charts.length"
                />
            </g>
        </svg> -->
    </div>
</template>

<script>
import {
    select as d3Select,
    scaleBand as d3ScaleBand,
    scaleLinear as d3ScaleLinear
} from 'd3'
export default {
    name: "d3-chart",
    props: {},
    data() {
        return {
            charts: []
        };
    },
    methods: {
        generateData() {
            this.charts = [
                Math.random() * 400,
                Math.random() * 400,
                Math.random() * 400,
                Math.random() * 400,
                Math.random() * 400
            ];
        },
        updateChart (wrapper, curData) {
            if(!wrapper){
                // error
                return
            }

            const DURATION = 1000;
            const width = 500;
            const height = 500;
            const margin = {top: 50, right: 50, bottom: 50, left: 50};
            const innerWidth = width - margin.left - margin.right;
            const innerHeight = height - margin.top - margin.bottom;

            const svgData = d3Select(wrapper).selectAll('svg').data(['data']);
            const svgEnter = svgData.enter().append('svg');
            svgEnter.attr('width', width);
            svgEnter.attr('height', height);

            svgEnter.append('g')
                .attr('transform', `translate(${margin.left}, ${margin.top})`);

            //merge
            const svgMerge = svgData.merge(svgEnter);
            const gMerge = svgMerge.select('g');

            let indexs = Array.from({ length : curData.length}, (d, index) => index);
            let x = d3ScaleBand().range([0, innerWidth]).domain(indexs);
            let y = d3ScaleLinear().range([innerHeight, 0]).domain([0, 400]);

            const rectData = gMerge.selectAll('rect').data(curData);
            const rectEnter = rectData.enter()
                .append('rect')
                .attr('fill', 'blue')
                .attr('x', (d, index) => x(index))
                .attr('y', (d) => y(d))
                // .attr('x', (d, index) => index * innerWidth / curData.length)
                // .attr('y', (d) => innerHeight - d)
                .attr('width', innerWidth / curData.length)
                .attr('height', (d) => d)

            const rectMerge = rectData.merge(rectEnter)
            rectMerge
                .transition()
                .duration(DURATION)
                .attr('y', (d) => y(d))
                .attr('height', (d) => d)
        }
    },
    mounted() {
        this.generateData();
        this.updateChart(this.$refs.$wrapper, this.charts);
        this.intervalId = setInterval(() => {
            this.generateData();
            this.updateChart(this.$refs.$wrapper, this.charts);
        }, 5000)
    },
    beforeDestroy () {
        clearInterval(this.intervalId)
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
