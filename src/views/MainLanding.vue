<template>
  <div class="div-center flex flex-col">
    <!-- view section -->
    <section>
   </section> 

    <section class="flex flex-row">

      <ListEventManager 
        :listItem="treeNotable" 
        @addData="treeNotable($event)" 
        @deleteData="delTreeNotable($event, value)"
        input-type="tree"
      />
      <ListEventManager 
        :listItem="requiredNotable" 
        @addData=" requiredNotable($event)" 
        @deleteData="delRequiredNotable($event)"
        input-type="required"
      />
      <ListEventManager 
        :listItem="optionalNotable" 
        @addData=" optionalNotable($event)" 
        @deleteData="delOptionalNotable($event)"
        input-type="optional"
      />

    </section>
    <section>
      <FileReader 
        :wanted="submitAllValue().required" 
        :ideal="submitAllValue().optional"
        :notables="submitAllValue().tree"
      ></FileReader>
    </section>
  </div> 
</template>

<script>
import { ref, computed } from 'vue'
import FileReader from '../components/FileReader.vue'
import ListEventManager from '../components/ListEventManager.vue'

export default {
  components: {
    FileReader,
    ListEventManager,
  },
  setup(){
    const notable = ref('')
    const notables = ref([])

    const tree = []
    const optional = []
    const required = []

    const addNotables = computed(() => {
      return notables.value
    })  
    
    const requiredNotable = (val) =>  {
      required.push(val)
      return val
    }
    const optionalNotable = (val) => {
      optional.push(val)
      return val
    }
    const treeNotable = (val) => {
      console.log(val)
      tree.push(val)
      return val
    }

    const submitAllValue = () => {
      return {tree, required, optional}
    } 

    const delTreeNotable = (index) => {
      console.log(index)
      tree.splice(index, 1)
    }

    const delRequiredNotable = (index) => {
      console.log(index)
      required.splice(index, 1)
    }

    const delOptionalNotable = (index) => {
      console.log(index)
      optional.splice(index, 1)
    }

    return {
      notable,
      notables,
      addNotables,
      requiredNotable,
      optionalNotable,
      treeNotable,
      submitAllValue,
      delTreeNotable,
      delOptionalNotable,
      delRequiredNotable,
    }
  }  
}
</script>

<style>

</style>
