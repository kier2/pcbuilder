<script setup>
  import { ref, onMounted, computed, watch } from "vue";
  import InfoModal from "./utils/InfoModal.vue";
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

    <InfoModal
      :show="openPopup"
      :info="selectedInfoParts"
      @close="openPopup = false"
    />
  </div>
</template>
