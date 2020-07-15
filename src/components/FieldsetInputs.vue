<template>
    <div class="d3param-input__box">
        <fieldset class="d3param-input__item" v-for="(input, input_key) in inputs" :key="input_key">
            <label class="d3param-input__label"><b class="darkBlue">{{input.label}}</b>{{input.unit ? ', '+input.unit : ''}}</label>
            <input v-if="input.type == 'text' || input.type == 'number'" 
                v-model="input.value" 
                :class="input.class" 
                :type="input.type" 
                :name="input.name+'['+input_key+']'"
            >
            <div v-else-if="input.type == 'select'" class="selectInner d3param-input__selectInner">
                <select v-model="input.selected" :disabled="input.disabled" :class="input.class" :name="block_id+'_field_'+input_key">
                    <option 
                        v-for="option in input.options" :key="option" 
                        :value="option.value" 
                        :disabled="option.disabled" 
                        :selected="option.selected" 
                    >{{option.text}}</option>
                </select>
                <i class="triangle selectTriangle"></i>
            </div>
        </fieldset>
        <fieldset class="d3param-control__box">
            <span class="d3param-control d3param-control__remove" @click="delRow(index)"><i class="d3param-control__circle">-</i> Удалить группу</span>
            <span class="d3param-control d3param-control__add" @click="addRow"><i class="d3param-control__circle">+</i> Добавить группу</span>
        </fieldset>
    </div>
</template>


<script>

export default {
	name: 'fieldset-inputs',
	props: {
        inputs: {
            type: Object
        },
        block_id: {
            required: true
        },
        index:{
            required: true
        },
        items: {
            type: Array
        }
    },
    methods: {
        addRow() {
            this.$emit('addrows', {index:this.index, id:this.block_id});
        },
        delRow(indexRow) {
            this.$emit('delrows', indexRow);
        },
        /**
         * Function for obtaining coordinates by clicking on the background image
         */
        coordinationsInit(){

        }
    },
    mounted() {
        this.coordinationsInit()
    }
}

</script>

<style scoped>
.d3param-input__label {
   max-width: 237px;
    line-height: 20px;
    display: inline-block;
    min-width: 30px;
    padding: 0px 10px;
    vertical-align: middle;
}
.coordinates{
    width: 65px;
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
</style>