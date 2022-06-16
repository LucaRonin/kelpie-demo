<script>
  import "./app.css";
  import R from "ramda";

  import { getConveNecessary } from "./conve_necessary.svelte";
  import { getConveSufficient } from "./conve_sufficient.svelte";
  import { getTranseNecessary } from "./transe_necessary.svelte";
  import { getTranseSufficient } from "./transe_sufficient.svelte";
  import { getComplexNecessary } from "./complex_necessary.svelte";
  import { getComplexSufficient } from "./complex_sufficient.svelte";

  const concatAll = R.unapply(R.reduce(R.concat, []));

  let data = concatAll(
    getConveNecessary(),
    getConveSufficient(),
    getTranseNecessary(),
    getTranseSufficient(),
    getComplexNecessary(),
    getComplexSufficient()
  );

  let model = "ConvE";
  let dataset = "FB15k";
  let scenario = "necessary";

  let predictions = data;
  let currentPrediction = JSON.parse(
    '{"prediction": {"head": {                        "entity": "Holly Marie Combs",                        "description": "American actress"                    },                    "relation": "has been friends with",                    "tail": {                        "entity": "Ben Affleck",                        "description": "American actor"                    }                },                "explanation": {                    "facts": [                        {                            "head": {                                "entity": "Ben Affleck",                                "description": "American actor"                            },                            "relation": "has been friends with",                            "tail": {                                "entity": "Holly Marie Combs",                                "description": "American actress"                            }                        }                    ],                    "old_rank": 1,                    "new_rank": 689,                    "old_score": 10.693,                    "new_score": 2.616,                    "note": "classica relazione inversa di FB15k"                }            }'
  );

  $: {
    predictions =
      R.find(
        R.allPass([
          R.propEq("model", model),
          R.propEq("dataset", dataset),
          R.propEq("scenario", scenario)
        ]),
        data
      ) || {};
  }

  $: {
    console.log(currentPrediction);
  }
</script>


<div class="min-h-full">
  <nav class="border-b border-gray-200 bg-white">
    <div class="mx-auto max-w-5xl px-4 sm:px-6 lg:px-8">
      <div class="flex h-16 justify-between">
        <div class="flex">
          <div class="hidden sm:-my-px sm:flex sm:space-x-8">
            <a href="#" class="inline-flex items-center border-b-2 border-indigo-500 pt-1 text-sm font-medium text-gray-900" aria-current="page"> Kelpie Sandbox </a>
          </div>
        </div>
      </div>
    </div>
  </nav>
  <div class="h-full bg-gray-200">
    <!-- PREDICTED LINKS -->
    <div class="mx-auto flex max-w-5xl flex-col items-center py-10">
      <div class="w-full px-4 sm:px-6 lg:px-8">
        <h1 class="text-3xl font-bold leading-tight text-gray-900">Predicted Links</h1>
      </div>
      <div class="w-full">
        <div class="mx-auto my-8 flex max-w-5xl flex-col gap-10 sm:px-6 lg:px-8">
          <!-- SELECT -->
          <div class="flex flex-row items-end gap-4">
            <div class="flex w-full flex-col items-start gap-2">
              <label for="location" class="w-40 text-sm font-medium text-gray-700">Model</label>
              <select bind:value={model} id="model" name="model" class="shadow mt-1 block w-full rounded-md border-gray-300 py-2 pl-3 pr-10 text-base focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 sm:text-sm">
                <option>ComplEx</option>
                <option selected>ConvE</option>
                <option>TransE</option>
              </select>
            </div>
            <div class="flex w-full flex-col items-start gap-2">
              <label for="location" class="w-40 text-sm font-medium text-gray-700">Dataset</label>
              <select bind:value={dataset} id="dataset" name="dataset" class="shadow mt-1 block w-full rounded-md border-gray-300 py-2 pl-3 pr-10 text-base focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 sm:text-sm">
                <option>FB15k</option>
                <option>WN18</option>
                <option>FB15k-237</option>
                <option>WN18RR</option>
                <option>YAG03-10</option>
              </select>
            </div>
            <div class="flex w-full flex-col items-start gap-2">
              <label for="location" class="w-40 text-sm font-medium text-gray-700">Expl. Type</label>
              <select bind:value={scenario} id="scenario" name="scenario" class="shadow mt-1 block w-full rounded-md border-gray-300 py-2 pl-3 pr-10 text-base focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 sm:text-sm">
                <option value="necessary" selected>Necessary</option>
                <option value="sufficient" >Sufficient</option>
              </select>
            </div>
          </div>
          <!-- TABLE -->
          <div class="flex grow flex-col">
            <div class="-my-2 -mx-4 overflow-x-auto sm:-mx-6 lg:-mx-8">
              <div class="inline-block min-w-full py-2 align-middle md:px-6 lg:px-8">
                <div class="relative overflow-hidden shadow ring-1 ring-black ring-opacity-5 md:rounded-lg">
                  <table class="min-w-full table-fixed divide-y divide-gray-300">
                    <thead class="bg-gray-100">
                      <tr>
                        <th scope="col" class="relative w-12 px-6 sm:w-16 sm:px-8"></th>
                        <th scope="col" class="min-w-[12rem] py-4 pr-3 text-left text-sm font-semibold text-gray-900">Head Entity</th>
                        <th scope="col" class="px-3 py-4 text-left text-sm font-semibold text-gray-900">Relation</th>
                        <th scope="col" class="px-3 py-4 text-left text-sm font-semibold text-gray-900">Tail Entity</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-gray-200 bg-white">
                      <!-- Selected: "bg-gray-50" -->
                      {#each (predictions.explained_predictions || []) as p}
                      <tr>
                        <td class="relative w-12 px-6 sm:w-16 sm:px-8">
                          <input bind:group={currentPrediction} value={p} name="link-explanation" type="radio" class="absolute left-4 top-1/2 -mt-2 h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500 sm:left-6" />
                        </td>
                        <!-- Selected: "text-indigo-600", Not Selected: "text-gray-900" -->
                        <td class="max-w-xs truncate whitespace-nowrap py-4 pr-3 text-sm font-medium text-gray-900">{R.path(['prediction', 'head', 'entity'], p)}</td>
                        <td class="max-w-xs truncate whitespace-nowrap px-3 py-4 text-sm text-gray-500">{R.path(['prediction', 'relation'], p)}</td>
                        <td class="max-w-xs truncate whitespace-nowrap px-3 py-4 text-sm text-gray-500">{R.path(['prediction', 'tail', 'entity'], p)}</td>
                      </tr>
                      {/each}
                    </tbody>

                  </table>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- KELPIE EXPLANATION -->
    <div class="mx-auto max-w-5xl px-4 pb-40 sm:px-6 lg:px-8">
      <h1 class="text-3xl font-bold leading-tight text-gray-900">Kelpie Explanation</h1>
      <!-- EXPLAINED LINK -->
      <h1 class="mt-8 text-xl leading-tight text-gray-600">Prediction to explain</h1>
      <div class="mt-4 flex flex-row justify-center items-center gap-4">
        <div class="max-w-5xl w-full grow">
          <div class="flex flex-col justify-center h-32 grow rounded-lg border-2 border-indigo-400 bg-white px-4 py-5 sm:px-6">
            <h3 class="text-lg font-medium leading-6 text-gray-900">{currentPrediction.prediction.head.entity}</h3>
            <p class="mt-1 text-sm text-gray-400">{currentPrediction.prediction.head.description}</p>
          </div>
        </div>
        <div>
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="#818CF8" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
          </svg>
        </div>
        <div class="w-full flex h-32 flex-row grow items-center rounded-lg border-2 border-indigo-400 bg-white px-4 sm:px-6">
          <div class="w-full text-lg">{currentPrediction.prediction.relation}</div>
        </div>
        <div>
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="#818CF8" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
          </svg>
        </div>
        <div class="flex flex-col justify-center grow w-full h-32 rounded-lg border-2 border-indigo-400 bg-white px-4 py-5 sm:px-6">
          <h3 class="text-lg font-medium leading-6 text-gray-900">{currentPrediction.prediction.tail.entity}</h3>
          <p class="mt-1 text-sm text-gray-400">{currentPrediction.prediction.tail.description}</p>
        </div>
      </div>

      <h1 class="mt-8 text-xl leading-tight text-gray-600">Extracted Explanation</h1>

      <div class="mt-4 flex flex-row gap-16">
        <!-- TABLE -->
        <div class="flex grow w-2/3 flex-col">
          <div class="-mx-4 overflow-x-auto sm:-mx-6 lg:-mx-8">
            <div class="inline-block min-w-full py-2 align-middle md:px-6 lg:px-8">
              <div class="shadow overflow-hidden ring-1 ring-black ring-opacity-5 md:rounded-lg">
                <table class="min-w-full divide-y divide-gray-300">
                  <thead class="bg-gray-100">
                    <tr>
                      <th scope="col" class="py-4 pl-4 pr-3 text-left text-sm font-semibold text-gray-900 sm:pl-6">Head Entity</th>
                      <th scope="col" class="px-3 py-4 text-left text-sm font-semibold text-gray-900">Relation</th>
                      <th scope="col" class="px-3 py-4 text-left text-sm font-semibold text-gray-900">Tail Entity</th>
                    </tr>
                  </thead>
                  <tbody class="divide-y divide-gray-200 bg-white">
                    {#each (currentPrediction.explanation.facts || []) as fact}
                    <tr>
                      <td class="max-w-xs truncate py-4 pl-4 pr-3 text-sm font-medium text-gray-900 sm:pl-6">{fact.head.entity}</td>
                      <td class="max-w-xs truncate px-3 py-4 text-sm text-gray-500">{fact.relation}</td>
                      <td class="max-w-xs truncate px-3 py-4 text-sm text-gray-500">{fact.tail.entity}</td>
                    </tr>
                    {/each}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
        <div class="mt-4 flex flex-row grow-0 gap-8">
          <div class="flex flex-col gap-2">
            <p>Original Tail Rank:</p>
            <p>Tail Rank after removal:</p>
            <p>Original Score:</p>
            <p>Score after removal:</p>
          </div>
          <div class="flex flex-col gap-2">
            <p class="font-bold">{currentPrediction.explanation.old_rank}</p>
            <p class="font-bold">{currentPrediction.explanation.new_rank}</p>
            <p class="font-bold">{currentPrediction.explanation.old_score}</p>
            <p class="font-bold">{currentPrediction.explanation.new_score}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>