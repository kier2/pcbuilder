<script setup>
  import { ref, onMounted, computed } from "vue";
  import { Dialog, DialogPanel, DialogTitle, TransitionChild, TransitionRoot } from '@headlessui/vue'
  import { PhilippinePeso, DollarSign } from 'lucide-vue-next';

  // props
  const props = defineProps({
    selectedItem: Object,
    selectedParts: Object
  });

  // emits
  const  emit = defineEmits(['selectedParts', 'priceOfSelected']);

  // variables
  const allComponentData = ref({});
  const loading = ref(true);
  const error = ref(null);
  const openPopup = ref(false)
  const selectedInfoParts = ref({})
  const toggleCurrency = ref(false)

  // Database Actions
  const fetchData = async () => {
    try{
      const res = await fetch('/data/data.json');
      if(!res.ok){
        throw new Error(`HTTP error! status: ${res.status}`);
      }

      const data = await res.json();
      allComponentData.value = data
      // console.log(data)
    } catch (err) {
      error.value = 'Failed to load data.';
      console.error("Could not fetch data", err);
    } finally {
      loading.value = false;
    }
  }

  // Events
  const handleSelectedParts = (id, name, img, priceUsd, pricePhp ) => {
    emit('selectedParts', {
      selectedPartId: id,
      selectedComponent: props.selectedItem.slug,
      selectedComponentsPart: name,
      selectedComponentsPartImg: img,
    })

    emit('priceOfSelected', {
      component: props.selectedItem.slug,
      priceUsd: priceUsd,
      pricePhp: pricePhp
    })
  }

  onMounted(() => {
    fetchData();
  })

  const currentParts = computed(() => {
    if(loading.value || error.value || !props.selectedItem || !props.selectedItem.slug){
      return [];
    }
    const parts = allComponentData.value[props.selectedItem.slug];
    // console.log(parts)
    return parts || [];
  });

  const getInfoParts = (img, name, specs) => {
    openPopup.value = true
    selectedInfoParts.value.img = img
    selectedInfoParts.value.name = name
    selectedInfoParts.value.specs = specs
    return selectedInfoParts
  }

</script>

<template>
  <div class="h-screen overflow-y-auto overflow-x-hidden shadow sm:rounded-md w-full  md:h-screen md:sticky md:top-0 custom-scrollbar">
    <div class="md:pl-10 pr-3 py-2">
      <div class="flex items-center justify-between mb-5">
        <div>
          <h2 class="text-white capitalize text-2xl font-semibold">
            <span v-if="props.selectedItem">
              {{ props.selectedItem.slug }}
            </span>
          </h2>
        </div>
        <div
        v-if="currentParts.length > 0"
        class="flex gap-1.5 items-center">
          <div class="group relative inline-flex w-11 shrink-0 rounded-full bg-gray-300 p-0.5 inset-ring inset-ring-gray-900/5 outline-offset-2 outline-indigo-600 transition-colors duration-200 ease-in-out has-checked:bg-indigo-600 has-focus-visible:outline-2">
            <span class="relative size-5 rounded-full bg-gray-100 shadow-xs ring-1 ring-gray-900/5 transition-transform duration-200 ease-in-out group-has-checked:translate-x-5">
              <span class="absolute inset-0 flex size-full items-center justify-center opacity-100 transition-opacity duration-200 ease-in group-has-checked:opacity-0 group-has-checked:duration-100 group-has-checked:ease-out" aria-hidden="true">
                <svg class="size-3 text-gray-400" fill="none" viewBox="0 0 12 12">
                  <path d="M4 8l2-2m0 0l2-2M6 6L4 4m2 2l2 2" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
                </svg>
              </span>
              <span class="absolute inset-0 flex size-full items-center justify-center opacity-0 transition-opacity duration-100 ease-out group-has-checked:opacity-100 group-has-checked:duration-200 group-has-checked:ease-in" aria-hidden="true">
                <svg class="size-3 text-indigo-600" fill="currentColor" viewBox="0 0 12 12">
                  <path d="M3.707 5.293a1 1 0 00-1.414 1.414l1.414-1.414zM5 8l-.707.707a1 1 0 001.414 0L5 8zm4.707-3.293a1 1 0 00-1.414-1.414l1.414 1.414zm-7.414 2l2 2 1.414-1.414-2-2-1.414 1.414zm3.414 2l4-4-1.414-1.414-4 4 1.414 1.414z" />
                </svg>
              </span>
            </span>
            <input
            type="checkbox"
            class="absolute inset-0 appearance-none focus:outline-hidden cursor-pointer" aria-label="Use setting" name="setting" id="toggleCurr"
            v-model="toggleCurrency" />
          </div>
          <label
          for="toggleCurr"
          :class="toggleCurrency ? 'text-green-500' : 'text-gray-500'"
          >USD</label>
        </div>
      </div>

      <p v-if="loading">Loading parts...</p>
      <p v-else-if="error" style="color: red;">{{ error }}</p>

      <ul
        v-else-if="currentParts.length > 0"
        class="space-y-3">
        <li
          v-for="(part, index) in currentParts"
          :key="index"
          class="bg-gray-900/80 rounded text-white hover:ring-2 hover:ring-indigo-300">
            <!-- <div class="hidden group-hover:block absolute">
              <button
              class="text-red-300"
              type="button">
                Clear
              </button>
            </div> -->
            <div class="p-8 space-y-6">
              <div class="flex md:flex-row flex-col md:gap-3 gap-y-6">
                <div class="flex-1">
                  <p>{{ part.name }}</p>
                </div>
                <div>
                  <h4 class="flex items-center gap-1 justify-end">
                    <span v-if="toggleCurrency">
                      <DollarSign class="text-gray-400 size-4" />
                    </span>
                    <span v-else>
                      <PhilippinePeso class="text-gray-400 size-4" />
                    </span>
                    <span class="text-lg font-semibold">
                      {{ toggleCurrency ? part.usdPrice : part.phpPrice }}
                    </span>
                  </h4>
                </div>
              </div>
              <div class="w-full flex items-center justify-between">
                <button
                class="border border-indigo-400 cursor-pointer hover:bg-indigo-400 px-3 py-1.5 text-sm rounded"
                type="button"
                @click="getInfoParts(part.img, part.name, part.specs)">
                  Info
                </button>
                <button
                  class="border border-indigo-400 cursor-pointer hover:bg-indigo-400 px-3 py-1.5 text-sm rounded"
                  :class="props.selectedParts?.[props.selectedItem?.slug]?.id === part.id ? 'bg-indigo-400' : ''"
                  type="button"
                  @click="handleSelectedParts(part.id, part.name, part.img, part.usdPrice, part.phpPrice)">
                    {{ props.selectedParts?.[props.selectedItem?.slug]?.id === part.id ? 'Selected' : 'Select' }}
                </button>
              </div>
            </div>
        </li>
      </ul>
    </div>
  </div>
  <!-- Modal -->
   <div>
    <TransitionRoot as="template" :show="openPopup">
      <Dialog class="relative z-10" @close="openPopup = false">
        <TransitionChild as="template" enter="ease-out duration-300" enter-from="opacity-0" enter-to="opacity-100" leave="ease-in duration-200" leave-from="opacity-100" leave-to="opacity-0">
          <div class="fixed inset-0 bg-gray-700/75 transition-opacity" />
        </TransitionChild>

        <div class="fixed inset-0 z-10 w-screen overflow-y-auto">
          <div class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0">
            <TransitionChild as="template" enter="ease-out duration-300" enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95" enter-to="opacity-100 translate-y-0 sm:scale-100" leave="ease-in duration-200" leave-from="opacity-100 translate-y-0 sm:scale-100" leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95">
              <DialogPanel class="relative transform overflow-hidden rounded-lg bg-gray-900 px-4 pt-5 pb-4 text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-3xl sm:p-6">
                <button
                class="text-white absolute top-3 right-3 z-10 cursor-pointer"
                type="button"
                @click="openPopup = false">
                  <span class="text-4xl">&times;</span>
                </button>
                <div>
                  <div class="mx-auto flex size-12 items-center justify-center">
                    <!-- <CheckIcon class="size-6 text-green-600" aria-hidden="true" /> -->
                     <img :src="selectedInfoParts.img" alt="">
                  </div>
                  <div class="mt-3 text-center sm:mt-5 text-white">
                    <DialogTitle as="h3" class="text-xl font-semibold">
                      {{ selectedInfoParts.name }}
                    </DialogTitle>
                    <div class="mt-5">
                      <table class="relative min-w-full">
                        <thead>
                          <tr>
                            <th scope="col" class="py-3.5 pr-3 pl-4 text-left text-sm font-semibold text-white sm:pl-0">
                              <span class="sr-only">Name</span>
                            </th>
                            <th scope="col" class="px-3 py-3.5 text-left text-sm font-semibold text-white">
                              <span class="sr-only">Specs</span>
                            </th>
                          </tr>
                        </thead>
                        <tbody class="divide-y divide-white/10">
                          <tr v-for="(spec, index) in selectedInfoParts.specs" :key="index">
                            <td class="py-4 pr-3 pl-4 text-sm font-medium whitespace-nowrap text-gray-400 sm:pl-0 text-left">
                              {{ spec.label }}
                            </td>
                            <td class="pl-6 px-3 py-4 text-sm text-white text-left">
                              {{ spec.value }}
                            </td>
                          </tr>
                        </tbody>
                      </table>
                    </div>
                  </div>
                </div>
                <div class="mt-5 sm:mt-6 flex justify-end">
                  <button type="button" class="inline-flex justify-center rounded px-3 py-2 text-sm font-semibold text-white shadow-xs sm:col-start-2 ring-2
                  hover:bg-white hover:text-gray-900/80 cursor-pointer" @click="openPopup = false">Close</button>
                </div>
              </DialogPanel>
            </TransitionChild>
          </div>
        </div>
      </Dialog>
    </TransitionRoot>
  </div>
</template>
