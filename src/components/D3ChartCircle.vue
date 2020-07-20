<template>
    <div>
        <div class="chars-border"
            ref="$wrapper"
            @mousedown='dndEvent'
            @mouseup='dndEventOff'
        ></div>
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
        dndEventOff(event) {
            if(event.target.classList.contains('chart-item')){
                const idx = +event.target.dataset.idx;
                console.log(idx);
                const parentBox = this.$el.querySelector('.bars-chart');
                // обновить items
                this.items[idx].inputs.x.value = event.offsetX;
                this.items[idx].inputs.y.value = event.offsetY;

                for (const child of parentBox.children) {
                    // console.log(child);
                }
            }
            this.$el.removeEventListener('mousemove', this.onMouseMove);
        },
        onMouseMove(event) {
            if(event.target.classList.contains('chart-item')){
                const el = event.target;
                const idx = +el.dataset.idx;
                this.moveChart(idx, event.offsetX, event.offsetY);
                el.onmouseup = null;
            }
        },
        moveChart(idx, offsetX, offsetY) {
            const elChart = this.$el.querySelector('.item-control__' + idx);
            const valChart = this.$el.querySelector('.value-control__' + idx);

            // графон
            elChart.setAttribute('cx', offsetX);
            elChart.setAttribute('cy', offsetY);
            valChart.setAttribute('x', offsetX);
            valChart.setAttribute('y', offsetY);
        },
        dndEvent(event){
            if(event.target.classList.contains('chart-item')){
                const el = event.target;
                const idx = +el.dataset.idx;
                const parentBox = this.$el.querySelector('.bars-chart');
                const elChart = this.$el.querySelector('.item-control__' + idx);
                const valChart = this.$el.querySelector('.value-control__' + idx);

                parentBox.append(elChart);
                parentBox.append(valChart);
                // console.log(el);

                // el.style.position = 'absolute';
                // el.style.zIndex = 1000;
                
                // перемещение по экрану
                this.$el.addEventListener('mousemove', this.onMouseMove);
            }
        },
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
            
            for (let index = 0; index < curDatas.length; index++) {
                const element = curDatas[index];
                if(!wrapper.querySelector('.item-control__' + index)){
                    gMerge.append(svg).attr('class', 'item-control__' + index);
                }
                const elemData = gMerge.select(svg + '.item-control__' + index);
                elemData
                    .attr("cursor", 'pointer')
                    .attr('class', 'chart-item item-control__' + index)
                    .attr('fill', element.inputs.hasOwnProperty('color') ? element.inputs.color.value : '#000000')
                    .attr('cx', element.inputs.x.value ? element.inputs.x.value : 0)
                    .attr('cy', element.inputs.y.value ? element.inputs.y.value : 0)
                    .attr('r', element.inputs.r.value ? element.inputs.r.value : 0)
                    .attr('data-idx', index)
                    .attr('stroke', '#ffffff')
                    .attr('stroke-width', stroke)

                //value
                //value enter
                if(!wrapper.querySelector('.value-control__' + index)){
                    gMerge.append('text').attr('class', 'value-control__' + index);
                }
                const valueData = gMerge.select('text.value-control__' + index);
                valueData
                    .attr('class', 'chart-item chart-value value-control__' + index)
                    .attr('fill', '#ffffff')
                    .attr('data-idx', index)
                    .attr("cursor", 'pointer')
                    .attr("x", element.inputs.x.value ? element.inputs.x.value : 0)
                    .attr("y", element.inputs.y.value ? element.inputs.y.value : 0)
                    .attr("dx", -element.inputs.r.value / (element.inputs.value.value.length > 1 ? 1.5 : 3))
                    .attr("dy", element.inputs.r.value / (element.inputs.value.value.length > 1 ? element.inputs.value.value.length + 1 : element.inputs.value.value.length + 2))
                    .text(element.inputs.hasOwnProperty('value') && element.inputs.value.value ? element.inputs.value.value : '')
                    .attr("font-size", 
                        element.inputs.hasOwnProperty('value') && element.inputs.value.value
                        ? (element.inputs.value.value.length > 1 
                            ? element.inputs.r.value * 2.5 / element.inputs.value.value.length
                            : element.inputs.r.value / element.inputs.value.value.length
                        )
                        : ''
                    )
            }

            // const elemData = gMerge.selectAll(svg).data(curDatas);
            // // enter elements
            // console.log(elemData.enter());
            // const elemEnter = elemData.enter()
            //     .append(svg)
            //     .attr("cursor", 'pointer')
            //     .attr('class', (d, idx) => 'chart-item item-control__' + idx)
            //     .attr('fill', (d) => d.inputs.hasOwnProperty('color') ? d.inputs.color.value : '#000000')
            //     .attr('cx', (d) => d.inputs.hasOwnProperty('x') && d.inputs.x.value ? d.inputs.x.value : 0)
            //     .attr('cy', (d) => d.inputs.hasOwnProperty('y') && d.inputs.y.value ? d.inputs.y.value : 0)
            //     .attr('r', (d, idx) => d.inputs.hasOwnProperty('r') && d.inputs.r.value ? d.inputs.r.value : 0)
            //     .attr('data-idx', (d, idx) => idx)
            //     .attr('stroke', '#ffffff')
            //     .attr('stroke-width', stroke)

            // // merge elements
            // const elemMerge = elemData.merge(elemEnter);
            // elemMerge
            //     // .transition()
            //     // .duration(duration)
            //     .attr("cursor", 'pointer')
            //     .attr('class', (d, idx) => 'chart-item item-control__' + idx)
            //     .attr('fill', (d) => d.inputs.hasOwnProperty('color') ? d.inputs.color.value : '#000000')
            //     .attr('cx', (d) => d.inputs.hasOwnProperty('x') && d.inputs.x.value ? +d.inputs.x.value : 0)
            //     .attr('cy', (d) => d.inputs.hasOwnProperty('y') && d.inputs.y.value ? +d.inputs.y.value : 0)
            //     // .attr('id', (d, idx) => 'chart-item')
            //     .attr('data-idx', (d, idx) => idx)
            //     .attr('r', function(d){
            //         return +d.inputs.r.value + stroke / 2
            //     })
            //     // .attr('stroke', '#ffffff')
            //     // .attr('stroke-width', '2')
            

            // //texts
            // const textData = gMerge.selectAll('text').data(curDatas);
            // //text enter
            // const textEnter = textData.enter()
            //     .append('text')
            //     .attr('class', (d, idx) => 'chart-item chart-value value-control__' + idx)
            //     .attr('fill', '#ffffff')
            //     .attr("cursor", 'pointer')
            //     .attr('data-idx', (d, idx) => idx)
            //     .attr("x", (d) => d.inputs.hasOwnProperty('x') && d.inputs.x.value ? d.inputs.x.value : 0)
            //     .attr("y", (d) => d.inputs.hasOwnProperty('y') && d.inputs.y.value ? d.inputs.y.value : 0)
            //     .text((d) => d.inputs.hasOwnProperty('value') && d.inputs.value.value ? d.inputs.value.value : '')
            //     // .attr('font-family', 'Leckerli One, cursive')
            // // text marge
            // const textMerge = textData.merge(textEnter);
            // textMerge
            //     // .transition()
            //     // .duration(duration)
            //     .attr('class', (d, idx) => 'chart-item chart-value value-control__' + idx)
            //     .attr('fill', '#ffffff')
            //     .attr('data-idx', (d, idx) => idx)
            //     .attr("cursor", 'pointer')
            //     .attr("x", (d) => d.inputs.hasOwnProperty('x') && d.inputs.x.value ? d.inputs.x.value : 0)
            //     .attr("y", (d) => d.inputs.hasOwnProperty('y') && d.inputs.y.value ? d.inputs.y.value : 0)
            //     .attr("dx", (d) => -d.inputs.r.value / (d.inputs.value.value.length > 1 ? 1.5 : 3))
            //     .attr("dy", (d) => d.inputs.r.value / (d.inputs.value.value.length > 1 ? d.inputs.value.value.length + 1 : d.inputs.value.value.length + 2))
            //     .text((d) => d.inputs.hasOwnProperty('value') && d.inputs.value.value ? d.inputs.value.value : '')
            //     .attr("font-size", (d) => 
            //         d.inputs.hasOwnProperty('value') && d.inputs.value.value
            //             ? (d.inputs.value.value.length > 1 
            //                 ? d.inputs.r.value * 2.5 / d.inputs.value.value.length
            //                 : d.inputs.r.value / d.inputs.value.value.length
            //             )
            //             : ''
            //         )

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
