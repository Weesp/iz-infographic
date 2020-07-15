<template>
	<div class="params-form">
		<div class="params-charts" :id="'item-'+item.id">
			<div class="params-charts__items">
				<fieldset-inputs 
					:index="index" 
					:inputs="item.inputs"
					:block_id="item.id"
					:items="items"
					@addrows="addrows($event)"
					@delrows="delrows($event)"
				></fieldset-inputs>
			</div>
		</div>
	</div>
</template>

<script>
import Inputs from '@/data/inputs.json'
import FieldsetInputs from '@/components/FieldsetInputs.vue'

export default {
	name: 'd3-params-chart',
	components: {
		FieldsetInputs,
	},
	props: {
		block_id: {
			type: Number
		},
		index: {
			type: Number
		},
		items: {
			type: Array
		}
	},
	data() {
        return {
			inputsProps: JSON.parse(JSON.stringify(Inputs["circle"].inputs)),
            item:{
				id: this.block_id,
				inputs: ( Object.keys(this.items[this.block_id].inputs).length ? this.items[this.block_id].inputs : JSON.parse(JSON.stringify(Inputs["circle"].inputs)))
			},
			tamplate: Inputs["circle"]
		};
	},
	watch: {
        item: {
            handler(val){
				const inputs = val.inputs;
				let keysInputs = Object.keys(inputs);
				let check = true;
				for (let index = 0; index < keysInputs.length; index++) {
					let key = keysInputs[index];
					if(!inputs[key].value){
						check = false;
						break;
					}
				}
				if(check){
					this.items[this.index].inputs = inputs;
				}
            },
            deep: true
        },
	},
	computed:{

	},
    methods:{
        addrows(prop){
            this.$emit('addrows', prop);
        },
        delrows(prop){
            this.$emit('delrows', prop);
		},
	}
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.params-charts__items{
	display: flex;
	flex-wrap: wrap;
}
</style>
