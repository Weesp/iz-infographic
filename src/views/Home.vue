<template>
	<div class="home">
		<nav-bar />
		<d3-params-chart
			v-for="(item, item_key) in items"
			:key="item_key"
			:block_id="item.block_id"
			:index="item_key"
			:items="items"
			@addrows="addrows($event)"
			@delrows="delrows($event)"
		/>
		<d3-chart-circle
			:items="items"
			:template="templates['circle'].svg"
		/>
	</div>
</template>

<script>
// @ is an alias to /src
import NavBar from '@/components/NavBar.vue'
import D3ParamsChart from '@/components/D3ParamsChart.vue'
import D3ChartCircle from '@/components/D3ChartCircle.vue'
import Inputs from '@/data/inputs.json'

export default {
	name: 'Home',
	components: {
		D3ParamsChart,
		D3ChartCircle,
		NavBar
	},
	data(){
		return {
			items: [{
				block_id: 0,
				inputs: {},
			}],
			range: 0,
			templates: Inputs
		}
	},
	methods: {
        addrows(params){
            this.items.push({
                block_id: params.id+1, 
                inputs: {},
            });
        },
        delrows(index) {
            if(this.items.length > 1){
                if (confirm("Вы действительно хотите удалить параметр с карты?")){
                    this.items.splice(index, 1);
                }
            }else{
                console.log("error: Удаление невозможно - достигнуто минимальное количество элементов.");
            }
        },
        allReset() {
            this.items = [{
                block_id: 0,
                inputs: {},
            }];
            return false;
        }
	},
	computed:{
        calculateElements(){
            return {
				// x: this.item.inputs.x, 
				// y: this.item.inputs.y,
				// weight: this.item.inputs.weight
			};
        }
    }
}
</script>

<style>

</style>