<template>
	<div class="home">
		<nav-bar />
		<div class="params-workspace">
			<h3>Параметры рабочего поля</h3>
			<div class="params-workspace__box">
				<div class="d3param-input__box">
					<fieldset class="d3param-input__item">
						<label data-v-64a15ee1="" class="d3param-input__label">
							<b class="darkBlue">Ширина</b>
						</label>
						<input v-model="workspace.width" name="w-width" type="number" class="input chart-params__input">
					</fieldset>
					<fieldset class="d3param-input__item">
						<label data-v-64a15ee1="" class="d3param-input__label">
							<b class="darkBlue">Высота</b>
						</label>
						<input v-model="workspace.height" name="w-height" type="number" class="input chart-params__input">
					</fieldset>
					<fieldset class="d3param-input__item">
						<label data-v-64a15ee1="" class="d3param-input__label">
							<b class="darkBlue">Фоновое изображение</b>
						</label>
						<input @change="previewThumbnail" type="file" name="w-image" class="chart-params__input">
					</fieldset>
				</div>
			</div>
		</div>
		<d3-chart-circle
			:items="items"
			:workspace="workspace"
			:template="template['circle'].svg"
		/>
		<div class="params-chart">
			<span class="d3param-control d3param-control__add" @click="addRow"><i class="d3param-control__circle">+</i> Добавить инфографик</span>
			<!--h3>Параметры инфографики</h3-->
			<d3-params-chart
				v-for="(item, item_key) in items"
				:key="item_key"
				:block_id="item.block_id"
				:index="item_key"
				:item="item"
				@peackcoords="peackcoords($event)"
				@addrows="addrows($event)"
				@delrows="delrows($event)"
			/>
		</div>
		<pre>{{items}}</pre>
	</div>
</template>

<script>
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
				inputs: JSON.parse(JSON.stringify(Inputs["circle"].inputs)),
			}],
			workspace: {
				width: 680,
				height: 360,
				image: '',
			},
			templates: Object.keys(Inputs),
			template: Inputs,
			index: 0,
			ids: [],
		}
	},
	methods: {
        addRow(){
            this.items.unshift({
                block_id: ++this.index, 
                inputs: JSON.parse(JSON.stringify(Inputs["circle"].inputs)),
            });
		},
		peackcoords(params){
			console.log(params);
			const field = this.$el.querySelector('.chars-border');
			field.style.cursor = "pointer";
			console.log(field);
        },
        delrows(index) {
			console.log(index);
            if(this.items.length > 1){
                if (confirm("Вы действительно хотите удалить параметр с карты?")){
					this.items.splice(index, 1);
					this.$el.querySelector('.item-control__' + index).remove();
                }
            }else{
				if (confirm("error: Удаление невозможно - достигнуто минимальное количество элементов.")){
					console.log("error: Удаление невозможно - достигнуто минимальное количество элементов.");
				}
            }
        },
        allReset() {
            this.items = [{
                block_id: 0,
                inputs: {},
            }];
            return false;
		},
		previewThumbnail(event){
			const input = event.target;
            if (input.files && input.files[0]) {
				const reader = new FileReader();
				const workspace = this.workspace;
                reader.onload = function(e) {
					workspace.image = e.target.result;
                }
                reader.readAsDataURL(input.files[0]);
            }
		}
	},
	watch: {
        workspace: {
            handler(val){
				console.log(val);
            },
            deep: true
		},
	},
	computed:{
        calculateElements(){
            return {
				// x: this.item.inputs.x, 
				// y: this.item.inputs.y,
				// width: this.item.inputs.width
			};
		},
		// reverseItems() {
        // 	return this.items.slice().reverse();
		// },
		showDialogCancelMatch() {
			return this.$store.state.reverseItems;
		}
    }
}
</script>

<style>
body{
	max-width: 930px;
    margin: 0px auto;
}
fieldset {
    border: 0;
    margin: 0;
    padding: 0;
}
h3{
	color: #0093d5;
    margin: 15px 0;
    font-size: 20px;
}
.darkBlue {
    color: #00415e;
}
.input, .textarea {
    background: #fff;
    border: 1px solid #00415e;
    color: #00415e;
    padding: 5px 10px;
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
.d3param-input__item {
	margin-bottom: 15px;
	display: initial;
}
.d3param-input__item:last-child {
    margin-bottom: 0;
}
.d3param-control {
    cursor: pointer;
    color: #0093d5;
    font-weight: 700;
    font-size: 14px;
    line-height: 16px;
    margin: 3px 10px;
    display: inline-block;
}
.d3param-control__circle{
    background: #0093d5;
    color: #fff;
    border-radius: 50%;
    font-weight: 700;
    width: 22px;
    padding: 3px 0;
    display: inline-block;
    text-align: center;
    font-size: 14px;
    font-style: normal;
    margin-right: 5px;
    line-height: 15px;
    cursor: pointer;
}
.params-chart .d3param-control__add{
	margin: 15px 0px;
}
.d3param-input__label {
   max-width: 237px;
    line-height: 20px;
    display: inline-block;
    min-width: 30px;
    padding: 0px 10px;
    vertical-align: middle;
}
</style>