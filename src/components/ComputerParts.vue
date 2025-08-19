<script setup>
  import { ref, onMounted, computed, watch } from "vue";
  import { Dialog, DialogPanel, DialogTitle, TransitionChild, TransitionRoot } from '@headlessui/vue'
  import { PhilippinePeso, DollarSign } from 'lucide-vue-next';

  // props
  const props = defineProps({
    selectedItem: Object,
    selectedParts: Object,
    isCurrencyUsd: Boolean
  });

  // emits
  const  emit = defineEmits(['selectedParts', 'priceOfSelected']);

  // variables
  const allComponentData = ref({});
  const loading = ref(true);
  const error = ref(null);
  const openPopup = ref(false)
  const selectedInfoParts = ref({})

  const selectedCpu = computed(() => props.selectedParts?.cpu);
  const selectedMotherboard = computed(() => props.selectedParts?.motherboard);

  const currentParts = computed(() => {
    if (loading.value || error.value || !props.selectedItem?.slug) {
      return [];
    }

    const partsList = allComponentData.value[props.selectedItem.slug] || [];

    switch (props.selectedItem.slug) {
      case 'motherboard':
        if (selectedCpu.value?.socket) {
          return partsList.filter(mb => mb.socket === selectedCpu.value.socket);
        }
        break;

      case 'cpu':
        if (selectedMotherboard.value?.socket) {
          return partsList.filter(cpu => cpu.socket === selectedMotherboard.value.socket);
        }
        break;

      case 'fan':
        if (selectedCpu.value?.socket) {
          return partsList.filter(fan => fan.compatibleCpuSockets?.includes(selectedCpu.value.socket));
        }
        break;
    }

    return partsList;
  });
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
    const selectedPart = allComponentData.value[props.selectedItem.slug]?.find(p => p.id === id);

    emit('selectedParts', {
      selectedPartId: id,
      selectedComponent: props.selectedItem.slug,
      selectedComponentsPart: name,
      selectedComponentsPartImg: img,
      priceUsd,
      pricePhp,
      socket: selectedPart?.socket,
      compatibleCpuSockets: selectedPart?.compatibleCpuSockets || []
    });

    emit('priceOfSelected', {
      component: props.selectedItem.slug,
      priceUsd,
      pricePhp
    });
  }

  onMounted(() => {
    fetchData();
  })

  const getInfoParts = (img, name, specs) => {
    openPopup.value = true
    selectedInfoParts.value.img = img
    selectedInfoParts.value.name = name
    selectedInfoParts.value.specs = specs
    return selectedInfoParts
  }

  watch(
    () => props.isCurrencyUsd,
    () => {
      console.log('Received: ', props.isCurrencyUsd)
    },{
      immediate: true
    }
  );

</script>

<template>
  <div class="h-screen overflow-y-auto overflow-x-hidden shadow sm:rounded-md w-full  md:h-[75%] md:sticky md:top-0 custom-scrollbar">
    <div class="md:pl-10 pr-3 py-2">
      <div class="flex items-center justify-between mb-5">
        <div>
          <h2 class="text-white uppercase text-2xl font-semibold">
            <span v-if="props.selectedItem">
              {{ props.selectedItem.slug }}
            </span>
          </h2>
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
            <div class="p-8 space-y-6">
              <div class="flex md:flex-row flex-col md:gap-3 gap-y-6">
                <div class="flex-1">
                  <p>{{ part.name }}</p>
                </div>
                <div>
                  <h4 class="flex items-center gap-1 justify-end">
                    <span v-if="props.isCurrencyUsd">
                      <DollarSign class="text-gray-400 size-4" />
                    </span>
                    <span v-else>
                      <PhilippinePeso class="text-gray-400 size-4" />
                    </span>
                    <span class="text-lg font-semibold">
                      {{ props.isCurrencyUsd ? part.usdPrice : part.phpPrice }}
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

    <div v-if="currentParts.length == 0"
    class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2">
        <h2 class="text-white text-center">Select Component First</h2>
    </div>
    <div v-if="currentParts.length === 0 && props.selectedItem.slug === 'motherboard' && selectedCpu">
      <h2 class="text-white text-center">No compatible motherboards found for this CPU</h2>
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
