<template>
  <div class="px-5">
    <!-- input section -->
    <section class="flex flex-col"></section>
    <section
      class="max-h-64 overflow-y-scroll overflow-x-hidden w-[24rem] flex justify-content-center"
    >
      <form class="block">
        <input
          type="text"
          class="border-2 border-sky-500 absolute flex div-center mb-10 w-64"
          v-model="filterNotable"
          @keyup="setFilterNotables(filterNotable)"
        />
        <br />
        <br />
        <selected v-if="inputType === 'tree'" class="">
          <option
            v-for="(treeNotables, index) in listOfNotables"
            class="text-center"
            :value="treeNotables"
            :key="index"
            @click.prevent="
              add(treeNotables);
              emitData(treeNotables);
            "
            ref="dropdownDataContent"
          >
            {{ treeNotables }}
          </option>
        </selected>
        <selected v-else ref="jewelDropdownList">
          <option
            v-for="(jewelNotables, index) in listOfNotables"
            class="text-center"
            :value="jewelNotables"
            :key="index"
            @click.prevent="
              add(jewelNotables);
              emitData(jewelNotables);
            "
            v-show="index > 2"
            ref="dropdownDataContent"
          >
            {{ jewelNotables }}
          </option>
        </selected>
      </form>
    </section>

    <section class="pt-5">
      <div class="text-left">
        <ul>
          <li v-for="(item, index) in listItem" :key="index" class="py-1">
            <button
              class="bg-rose-500 text-white rounded-md px-5 py-2"
              @click.prevent="
                del($event, index);
                emitDelete($event, index);
              "
            >
              del
            </button>
            {{ item }}
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
import { ref, defineEmits, computed } from 'vue';

export default {
  props: {
    // the list item within the display list of notables
    listItem: {
      type: Array,
      default: null,
      required: false,
    },
    // as a prop to convert by the api, referring to the dropdown menu
    listOfNotables: {
      type: Array,
      default: null,
      required: false,
    },
    // input type refers whether it is a tree or timeless notable
    inputType: {
      type: String,
      default: null,
      required: false,
    },
    // i forgot what this is for
    isRendered: {
      type: Boolean,
      default: false,
    },
  },
  emits: ['addData', 'deleteData'],
  setup(props) {
    const dropdownList = ref([]);
    const jewelDropdownList = ref([]);

    const item = ref('');
    const dropdownDataContent = ref('');

    const showDropdownList = computed(() => {
      return dropdownList.value !== '' && jewelDropdownList !== '';
    });
    const listItem = ref([]);
    const inputType = props.inputType;

    // when user add a new notable to the list
    const add = value => {
      // prevent from adding same valuesj
      if (!listItem.value.includes(value)) listItem.value.push(value);
    };

    // delete notable from the list
    const del = (e, index) => {
      listItem.value.splice(index, 1);
    };

    const setFilterNotables = e => {
      if (dropdownDataContent.value === null) return;

      let test = Object.assign({}, dropdownDataContent.value);
      for (const key in test) {
        if (test[key].value.toLowerCase().includes(e.toLowerCase()))
          test[key].classList.remove('hidden');
        else test[key].classList.add('hidden');
      }
    };

    return {
      listItem,
      item,
      inputType,
      add,
      del,
      dropdownList,
      jewelDropdownList,
      showDropdownList,
      setFilterNotables,
      dropdownDataContent,
    };
  },
  methods: {
    emitData(e) {
      // this.listItem[this.listItem.length - 1]
      this.$emit('addData', e);
    },
    emitDelete(e, index) {
      this.$emit('deleteData', index);
    },
  },
};
</script>

<style></style>
