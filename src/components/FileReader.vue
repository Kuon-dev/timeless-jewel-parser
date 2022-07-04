<template>
  <div>
    <!-- select timeless -->
    <form>
      <select
        class="text-2xl"
        v-model="selected"
        @change="selectedTimelessJewel()"
      >
        <option
          v-for="(value, index) in timelessJewels"
          :key="index"
          class="text-2xl"
          ref="index"
          :value="value.file"
        >
          {{ value.name }}
        </option>
      </select>

      <button class="bg-sky-500 py-2 text-white rounded-md" @click.prevent="submit()">Submit</button>
    </form>

    <section>
      <div>seed: </div>
      <div ref="render">{{ render }}</div>
    </section>
  </div>
</template>

<script>
// import BrutalRestraintSeeds from "/TimelessJewelData-main/BrutalRestraintSeeds.csv"
import Papa from 'papaparse';
import { ref, computed } from 'vue';

export default {
  props: {
    wanted: {
      type: Array,
      required: false,
    },
    ideal: {
      type: Array,
      required: false,
    },
    notables: {
      type: Array,
      required: false,
    },
  },
  setup(props) {
    const selected = ref([]);
    const render = ref(null)

    const selectedTimelessJewel = () => {
      return selected.value;
    };


    const submit = () => {
      const wanted = (props.wanted)
      const ideal = (props.ideal)
      const notables = (props.notables)
      const curr = selectedTimelessJewel();
      const idealCountArr = [];
      console.log(wanted, ideal, notables)

      fetch(curr)
        .then(response => response.text())
        .then(text => {
          Papa.parse(text, {
            header: true,
            complete: results => {
              results.data.map((rows, index) => {
                let wantedCount = 0;
                let idealCount = 0;

                notables.map(notable => {
                  const val = rows[notable];
                  if (wanted.includes(val)) wantedCount += 1;
                  else if (ideal.includes(val)) idealCount += 1;
                });

                if (wantedCount >= 1) {
                  console.log('pass')
                  idealCountArr.push({
                    seed: index + 10000,
                    count: idealCount,
                    required: wantedCount,
                  });
                }
              });
            },
          });
        });

      const setResult = setTimeout(() => {
        // get all the minimum required notables

        try {
          console.log(idealCountArr)
          if (idealCountArr.legnth < 1) throw 'couldnt find a timeless';
          // get the ideal optional notables
          const max = idealCountArr.reduce(function (prev, current) {
            return prev.count > current.count ? prev : current;
          });
          console.log('Best timeless');
          console.log(max)
          render.value.innerHTML = max.seed
          console.log(render)
        } catch (e) {
          console.log(e)
          console.log("couldn't find timeless");
        }
      }, 10000);
    };

    const timelessJewels = {
      brutalRestraint: {
        name: 'Brutal Restraint',
        file: '/TimelessJewelData-main/BrutalRestraintSeeds.csv',
      },
      lethalPride: {
        name: 'Lethal Pride',
        file: '/TimelessJewelData-main/LethalPrideSeeds.csv',
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

      // ['chance_to_deal_double_damage_%'];
      //['chance_to_intimidate_on_hit_%'];
      /*
      [
      'Herbalism',
      'Swift Venoms',
      'Master Fletcher',
      'Inveterate',
      'Careful Conservationist',
      'Acuity',
      'Fervour',
      'Survivalist',
    ];
       */

    return {
      timelessJewels,
      selectedTimelessJewel,
      selected,
      submit,
      render,
    };
  },
};
</script>

<style></style>
