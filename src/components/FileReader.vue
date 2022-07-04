<template>
  <div>
    <!-- select timeless -->
    <form class="flex flex-col gap-4">
      <label>Select required limit (recommended up to 3)</label>
      <input
        type="number"
        class="border-2 border-rose-500"
        v-model="thresholdLimit"
      />
      <button
        class="bg-sky-500 py-2 text-white rounded-md"
        @click.prevent="submitJson()"
      >
        Submit
      </button>
    </form>

    <section>
      <div>seed:</div>
      <div ref="render" v-show="isRendered">{{ seedNumber }}</div>
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
    currentSelectedTimeless: {
      type: String,
      required: false,
    },
  },
  setup(props) {
    const render = ref('');
    const thresholdLimit = ref(null);

    const isRendered = computed(() => {
      return render.value !== '';
    });

    let seedNumber = '';

    const submitJson = () => {
      console.log('Searching...');
      const wanted = props.wanted;
      const ideal = props.ideal;
      const notables = props.notables;
      const limit = thresholdLimit.value;
      // get current timeless
      const curr = props.currentSelectedTimeless;
      console.log(curr);
      const idealCountArr = [];
      if (
        wanted.length < 1 ||
        ideal.length < 1 ||
        wanted === null ||
        ideal === null
      ) {
        console.error('empty input detected');
      }

      fetch(curr)
        .then(response => response.json())
        .then(data => {
          data.map((rows, index) => {
            let wantedCount = 0;
            let idealCount = 0;

            notables.map(notable => {
              const val = rows[notable];
              if (wanted.includes(val)) wantedCount += 1;
              else if (ideal.includes(val)) idealCount += 1;
            });

            if (wantedCount >= limit) {
              idealCountArr.push({
                seed: index + 10000,
                count: idealCount,
                required: wantedCount,
              });
            }
          });
          const setResult = setTimeout(() => {
            // get all the minimum required notables

            try {
              if (idealCountArr.legnth < 1) throw 'couldnt find a timeless';
              // get the ideal optional notables
              const max = idealCountArr.reduce(function (prev, current) {
                return prev.count > current.count ? prev : current;
              });
              console.log('Best timeless');
              console.log(max);
              seedNumber = max.seed;
              render.value.innerHTML = max.seed;
            } catch (e) {
              console.log(e);
              console.log("couldn't find timeless");
            }
          }, 3000);
          console.log('other results');
          console.log(idealCountArr);
        });
    };

    const timelessJewels = {
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
      render,
      submitJson,
      thresholdLimit,
      isRendered,
      seedNumber,
    };
  },
};
</script>

<style></style>
