<template>
    <div>
        <div class="chars-border" ref="$wrapper"></div>
    </div>
</template>

<script>
import {
    select as d3Select,
    scaleBand as d3ScaleBand,
    scaleLinear as d3ScaleLinear,
    axisLeft as d3AxisLeft,
    axisBottom as d3AxisBottom
} from 'd3'
export default {
    name: "d3-chart-circle",
    props: {
        items: {
            type: Array
        },
        template: {
            type: String
        },
        workspace: {
            type: Object
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
        workspace: {
            handler(items){
                this.updateChart(this.$refs.$wrapper, items);
            },
            deep: true
        }
	},
    methods: {
        updateChart (wrapper, curDatas) {
            // d3 constructor
            if(!wrapper){
                // error
                return
            }
            const width = this.workspace.width;
            const height = this.workspace.height;
            const imageBase = this.workspace.image;
            const svg = this.template;
            const duration = 100;
            const stroke = 2;

            // создание поля Svg
            const svgData = d3Select(wrapper).selectAll('svg').data(['null data']);
            const svgEnter = svgData.enter().append('svg')
                .attr('width', width)
                .attr('height', height);

            // добавление фона
            // <image xlink:href="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFCAYAAACNbyblAAAAHElEQVQI12P4//8/w38GIAXDIBKE0DHxgljNBAAO9TXL0Y4OHwAAAABJRU5ErkJggg=="/>
            svgEnter.append('image')
                .attr('width', width)
                .attr('height', height)
                .attr('xlink:href', imageBase);

            //добавление обёртки g
            svgEnter.append('g').attr('class', 'bars-chart');

            //merge g and svg
            const svgMerge = svgData.merge(svgEnter);
            svgMerge.attr('width', width).attr('height', height);
            const gMerge = svgMerge.select('g.bars-chart');
            const imgMerge = svgMerge.select('image');
            imgMerge.attr('width', width)
                .attr('height', height)
                .attr('xlink:href', imageBase);
            
            const elemData = gMerge.selectAll(svg).data(curDatas);
            // enter elements

            const elemEnter = elemData.enter()
                .append(svg)
                .attr('class', (d, idx) => 'chart-item item-control__' + idx)
                .attr('fill', (d) => d.inputs.hasOwnProperty('color') ? d.inputs.color.value : '#000000')
                .attr('cx', (d) => d.inputs.hasOwnProperty('x') && d.inputs.x.value ? d.inputs.x.value : 0)
                .attr('cy', (d) => d.inputs.hasOwnProperty('y') && d.inputs.y.value ? d.inputs.y.value : 0)
                .attr('r', (d, idx) => d.inputs.hasOwnProperty('r') && d.inputs.r.value ? d.inputs.r.value : 0)
                .attr('stroke', '#ffffff')
                .attr('stroke-width', stroke)

            // merge elements
            const elemMerge = elemData.merge(elemEnter);
            elemMerge
                .transition()
                .duration(duration)
                .attr('class', (d, idx) => 'chart-item item-control__' + idx)
                .attr('fill', (d) => d.inputs.hasOwnProperty('color') ? d.inputs.color.value : '#000000')
                .attr('cx', (d) => d.inputs.hasOwnProperty('x') && d.inputs.x.value ? +d.inputs.x.value : 0)
                .attr('cy', (d) => d.inputs.hasOwnProperty('y') && d.inputs.y.value ? +d.inputs.y.value : 0)
                .attr('r', function(d){
                    return +d.inputs.r.value + stroke / 2
                })
                // .attr('stroke', '#ffffff')
                // .attr('stroke-width', '2')
            

            //texts
            const textData = gMerge.selectAll('text').data(curDatas);
            //text enter
            const textEnter = textData.enter()
                .append('text')
                .attr('class', (d, idx) => 'chart-value value-control__' + idx)
                .attr('fill', '#ffffff')
                .attr("x", (d) => d.inputs.hasOwnProperty('x') && d.inputs.x.value ? d.inputs.x.value : 0)
                .attr("y", (d) => d.inputs.hasOwnProperty('y') && d.inputs.y.value ? d.inputs.y.value : 0)
                .text((d) => d.inputs.hasOwnProperty('value') && d.inputs.value.value ? d.inputs.value.value : '')
                // .attr('font-family', 'Leckerli One, cursive')
            // text marge
            const textMerge = textData.merge(textEnter);
            textMerge
                .transition()
                .duration(duration)
                .attr('class', (d, idx) => 'chart-value value-control__' + idx)
                .attr('fill', '#ffffff')
                .attr("x", (d) => d.inputs.hasOwnProperty('x') && d.inputs.x.value ? d.inputs.x.value : 0)
                .attr("y", (d) => d.inputs.hasOwnProperty('y') && d.inputs.y.value ? d.inputs.y.value : 0)
                .attr("dx", (d) => -d.inputs.r.value / (d.inputs.value.value.length > 1 ? 1.5 : 3))
                .attr("dy", (d) => d.inputs.r.value / (d.inputs.value.value.length > 1 ? d.inputs.value.value.length + 1 : d.inputs.value.value.length + 2))
                .text((d) => d.inputs.hasOwnProperty('value') && d.inputs.value.value ? d.inputs.value.value : '')
                .attr("font-size", (d) => 
                    d.inputs.hasOwnProperty('value') && d.inputs.value.value
                        ? (d.inputs.value.value.length > 1 
                            ? d.inputs.r.value * 2.5 / d.inputs.value.value.length
                            : d.inputs.r.value / d.inputs.value.value.length
                        )
                        : ''
                    )

            // const elemSelect = 

            // gMerge.append('g').attr('class', 'axis-y').call(d3AxisLeft(width))
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
.chars-border{
    display: inline-block;
    border: 1px solid #ececec
}
.chart-value{

}
.chart-item{

}
</style>
