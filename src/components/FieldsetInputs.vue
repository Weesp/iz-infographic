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
            <div v-else-if="input.type == 'group'" class="group-fields">
                <div v-for="(group, group_key) in input.group"
				    :key="group_key"
                >
                    <input v-if="group.type == 'text' || group.type == 'number'" 
                        v-model="group.value" 
                        :class="group.class" 
                        :type="group.type" 
                        :name="group.name+'['+group_key+']'"
                    >
                </div>
            </div>
            <!--div v-if="input.template=='peacker'"
                class="d3param-input__item peacker d3param-control"
                @click="peackCoord(item)"
            >
                Выбрать XY
            </div-->
        </fieldset>
        <fieldset class="d3param-control__box">
            <span class="d3param-control d3param-control__remove" @click="delRow(index)"><i class="d3param-control__circle">-</i> Удалить </span>
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
        item: {
            type: Object
        }
    },
    methods: {
        delRow(indexRow) {
            this.$emit('delrows', indexRow);
        },
        peackCoord(prop){
            this.$emit('peackcoords', prop);
        }
        /**
         * Function for obtaining coordinates by clicking on the background image
         */
    },
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
</style>