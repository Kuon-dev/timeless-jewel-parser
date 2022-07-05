<template>
  <div class="div-center flex flex-col">
    <!-- view section -->
    <section>
      <select
        required
        class="text-2xl bg-gray-500"
        v-model="selectedJewel"
        @change="selectedTimelessJewel()"
      >
        <option
          v-for="(value, index) in listOfTimelessJewels"
          :key="index"
          class="text-2xl"
          ref="index"
          :value="value.file"
        >
          {{ value.name }}
        </option>
      </select>
      <button
        @click.prevent="renderJewelNotables()"
        class="text-2xl bg-sky-500"
      >
        Select timeless
      </button>
    </section>

    <section class="flex flex-row pt-10 justify-content-center">
      <ListEventManager
        :is-rendered="isRendered"
        :list-of-notables="treeNotableDropdownList"
        @addData="treeNotable($event)"
        @deleteData="delTreeNotable($event, value)"
        input-type="tree"
      />
      <ListEventManager
        :is-rendered="isRendered"
        :list-of-notables="timelessNotableDropdownList"
        @addData="requiredNotable($event)"
        @deleteData="delRequiredNotable($event)"
        input-type="required"
      />
      <ListEventManager
        :is-rendered="isRendered"
        :list-of-notables="timelessNotableDropdownList"
        @addData="optionalNotable($event)"
        @deleteData="delOptionalNotable($event)"
        input-type="optional"
      />
    </section>

    <section>
      <FileReader
        :wanted="submitAllValue().required"
        :ideal="submitAllValue().optional"
        :notables="submitAllValue().tree"
        :current-selected-timeless="submitAllValue().currentJewel"
      ></FileReader>
    </section>
  </div>
</template>

<script>
import { ref, computed } from 'vue';
import FileReader from '../components/FileReader.vue';
import ListEventManager from '../components/ListEventManager.vue';

export default {
  components: {
    FileReader,
    ListEventManager,
  },
  setup() {
    const notable = ref('');
    const notables = ref([]);
    let isRendered = ref(false);

    const listOfTimelessJewels = {
      brutalRestraint: {
        name: 'Brutal Restraint',
        file: '/TimelessJewelData-main/BrutalRestraintSeeds.csv',
      },
      lethalPride: {
        name: 'Lethal Pride',
        file: 'http://localhost:3000/Lethal_Pride',
      },
      militantFaith: {
        name: 'Militant Faith',
        file: '/TimelessJewelData-main/MilitantFaithSeeds.csv',
      },
      elegantHubris: {
        name: 'Elegant Hubris',
        file: '/TimelessJewelData-main/ElegantHubrisSeeds.csv',
      },
    };

    const selectedJewel = ref(listOfTimelessJewels.brutalRestraint.file);
    const selectedTimelessJewel = () => {
      return selectedJewel.value;
    };

    const tree = [];
    const optional = [];
    const required = [];

    const treeNotableDropdownList = ref(null);
    const timelessNotableDropdownList = ref(null);

    const addNotables = computed(() => {
      return notables.value;
    });

    const requiredNotable = val => {
      required.push(val);
      return val;
    };
    const optionalNotable = val => {
      optional.push(val);
      return val;
    };
    const treeNotable = val => {
      tree.push(val);
      return val;
    };

    const submitAllValue = () => {
      const currentJewel = selectedTimelessJewel();
      return { tree, required, optional, currentJewel };
    };

    const delTreeNotable = index => {
      tree.splice(index, 1);
    };

    const delRequiredNotable = index => {
      required.splice(index, 1);
    };

    const delOptionalNotable = index => {
      optional.splice(index, 1);
    };

    const listItemNotable = () => {
      fetch('./TimelessJewelData-main/LethalPrideSeeds.json')
        .then(response => response.json())
        .then(data => {
          console.log(data)
          console.log('fetching data....');
          const unwantedNotable = ['field1', 'field2'];
          const treeNotables = [];
          const listOfNotablesLen = Object.getOwnPropertyNames(data[0]).length;
          Object.keys(data[0]).forEach((key, index) => {
            if (
              !unwantedNotable.includes(key) &&
              index <= listOfNotablesLen - 14
            ) {
              treeNotables.push(key);
            }
          });
          console.log('data loaded');
          treeNotableDropdownList.value = treeNotables;
        })
        .catch(e => {
          console.log('Error: ' + e);
        });
    };

    const renderJewelNotables = () => {
      const currentJewel = selectedTimelessJewel();
      console.log('fetching timeless data');
      fetch(currentJewel)
        .then(response => response.json())
        .then(data => {
          console.log('fetching data....');
          const jewelNotables = [];
          Object.keys(data[1]).forEach((key, index) => {
            if (!jewelNotables.includes(data[1][key]) && index > 1) {
              jewelNotables.push(data[1][key]);
            }
          });

          console.log('data loaded');
          timelessNotableDropdownList.value = jewelNotables;
        })
        .catch(e => {
          console.log('Error: ' + e);
        });
    };

    console.log('loading presets...');
    listItemNotable();

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
      isRendered,
      treeNotableDropdownList,
      timelessNotableDropdownList,
      listOfTimelessJewels,
      selectedTimelessJewel,
      selectedJewel,
      renderJewelNotables,
    };
  },
};
</script>

<style></style>
