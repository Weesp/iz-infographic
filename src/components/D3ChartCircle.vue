<template>
    <div>
        <div ref="$wrapper">

        </div>
        <pre>{{items}}</pre>
    </div>
</template>

<script>
import {
    select as d3Select,
    scaleBand as d3ScaleBand,
    scaleLinear as d3ScaleLinear
} from 'd3'
export default {
    name: "d3-chart-circle",
    props: {
        items: {
            type: Array
        },
        template: {
            type: String
        }
    },
    data() {
        return {
            charts: []
        };
    },
    watch: {
        items: {
            handler(items){
                this.updateChart(this.$refs.$wrapper, items);
            },
            deep: true
        },
	},
    methods: {
        updateChart (wrapper, curDatas) {
            console.log(curDatas);
            if(!wrapper){
                // error
                return
            }

            const width = 500;
            const height = 500;
            const svg = this.template;
            const duration = 500

            // создание поля Svg
            const svgData = d3Select(wrapper).selectAll('svg').data(['null data']);
            const svgEnter = svgData.enter().append('svg');
            svgEnter.attr('width', width);
            svgEnter.attr('height', height);

            svgEnter.append('g');
            //merge
            const svgMerge = svgData.merge(svgEnter);
            const gMerge = svgMerge.select('g');
            
            const elemData = gMerge.selectAll(svg).data(curDatas);
            // enter elements
            const elemEnter = elemData.enter()
                .append(svg)
                .attr('cx', (d) => d.inputs.hasOwnProperty('x') ? d.inputs.x.value : 0)
                .attr('cy', (d) => d.inputs.hasOwnProperty('y') ? d.inputs.y.value : 0)
                .attr('r', (d, idx) => d.inputs.hasOwnProperty('r') ? d.inputs.r.value : 0)

            // merge elements
            const elemMerge = elemData.merge(elemEnter);
            elemMerge
                .transition()
                .duration(duration)
                .attr('cx', (d) => d.inputs.hasOwnProperty('x') ? d.inputs.x.value : 0)
                .attr('cy', (d) => d.inputs.hasOwnProperty('y') ? d.inputs.y.value : 0)
                .attr('r', (d, idx) => d.inputs.hasOwnProperty('r') ? d.inputs.r.value : 0)
            
            // for (const key in curDatas) {
            //     const curData = curDatas[key];
            //     console.log(curData);
            //     if(Object.keys(curData.inputs).length){
            //         let cx = curData.inputs.x.value ? curData.inputs.x.value : 0;
            //         let cy = curData.inputs.y.value  ? curData.inputs.y.value : 0;
            //         let r = curData.inputs.r.value  ? curData.inputs.r.value : 0;
            //         let circle = {cx, cy, r}
            //         console.log(cx, cy, r);
            //         const circleData = gMerge.selectAll('circle').data(circle);
            //         const circleEnter = circleData.enter()
            //             .append('circle')
            //             .attr('cx', cx)
            //             .attr('cy', cy)
            //             .attr('r', r)
            //     }
            // }
            
        }
    },
    mounted() {
        this.updateChart(this.$refs.$wrapper, this.items);
        // this.intervalId = setInterval(() => {
        //     this.generateData();
        //     this.updateChart(this.$refs.$wrapper, this.items);
        // }, 5000)
    },
    beforeDestroy () {
        clearInterval(this.intervalId)
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
