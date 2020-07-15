<template>
	<div class="params-form">
		<div class="params-workspace">
			<h3>Параметры рабочего поля</h3>
			<div class="params-workspace__box">
				<div class="d3param-input__box">
					<fieldset class="d3param-input__item">
						<label data-v-64a15ee1="" class="d3param-input__label">
							<b class="darkBlue">Ширина</b>
						</label>
						<input name="w-weight" type="number" class="input chart-params__input">
					</fieldset>
					<fieldset class="d3param-input__item">
						<label data-v-64a15ee1="" class="d3param-input__label">
							<b class="darkBlue">Высота</b>
						</label>
						<input name="w-height" type="number" class="input chart-params__input">
					</fieldset>
				</div>
			</div>
		</div>
		<div class="params-charts" :id="'item-'+item.id">
			<h3>Параметры инфографики</h3>
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
.d3param-input__box {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    min-width: 100%;
    margin-bottom: 26px;
    border-top: 1px solid #0093d5;
    border-bottom: 1px solid #0093d5;
    position: relative;
    padding: 14px 18px;
    box-sizing: border-box;
}
.d3param-input__box:after, .d3param-input__box:before {
    content: "";
    width: calc(100% - 2px);
    height: 10px;
    position: absolute;
    left: 0;
    border-right: 1px solid #0093d5;
    border-left: 1px solid #0093d5;
}
.d3param-input__box:before {
    top: 0;
}
.d3param-input__box:after {
    bottom: 0;
}
</style>
