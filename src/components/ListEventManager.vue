<template>
  <div class="px-5">
    <section>
      <div class="text-left">
        <ul>
          <li v-for="(item, index) in listItem" :key="index" class="py-1">
            <button 
              class="bg-rose-500 text-white rounded-md px-5 py-2"
              @click="del($event, index); emitDelete($event, index)"
            > 
              del 
            </button>
            {{ item }}
          </li>
        </ul>
      </div>
    </section>
    <!-- input section -->
    <section class="flex flex-col">
      <form class="flex flex-col">
        <label class="text-2xl" >{{ inputType }}</label>
        <input class="border-2 border-black" type="text" v-model="item">
        <button @click.prevent="add(); emitData()" class="bg-rose-500 text-white mt-5 mb-5 py-5"> {{ inputType }} notables</button>
      </form>
    </section> 

  </div>
</template>

<script>
import { ref, defineEmits } from 'vue'

export default {
  props: {
    list: {
      type: Array,
      default: null,
      required: false,
    },
    inputType: {
      type: String,
      default: null,
      required: false,
    } 

  },
  emits: ['addData', 'deleteData'],
  setup(props) {
    const listItem = ref([])
    const inputType = props.inputType

    const item = ref('')
    
    const add = () => {
      if (item.value === '' || item.value === null) return
      listItem.value.push(item.value)
    }

    const del = (e, index) => {
      listItem.value.splice(index, 1)
    }

    return {
      listItem,
      inputType,
      add,
      del,
      item,
    }
  },
  methods: {
    emitData(){
      this.$emit('addData', this.listItem[this.listItem.length - 1])
    },
    emitDelete(e, index){ 
      this.$emit('deleteData', index)
    },
  }

}
</script>

<style>

</style>
