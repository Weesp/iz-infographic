<template>
    <div>
        <fieldset class="page-calc__item" v-for="(input, input_key) in inputs" :key="input_key">
            <label class="page-calc__label"><b class="darkBlue">{{input.label}}</b>{{input.unit ? ', '+input.unit : ''}}</label>
            <input 
                v-if="input.type == 'text' || input.type == 'number'" 
                v-model="input.value" 
                :class="input.class" 
                :type="input.type" 
                :name="input.name+'['+input_key+']'"
            >
            <div v-else-if="input.type == 'select'" class="selectInner page-calc__selectInner">
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
        <fieldset class="page-calc__item mb0">
            <span class="page-calc__remove" @click="delRow(index)"><i class="page-calc__circle">-</i> Удалить группу</span>
            <span class="page-calc__add" @click="addRow"><i class="page-calc__circle">+</i> Добавить группу</span>
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
    }
}

</script>