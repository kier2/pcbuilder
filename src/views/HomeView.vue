<script setup>
import { ref, watch, computed } from "vue";
import ComputerPartsSection from "@/components/ComputerParts.vue";
import PcPeripherals from "@/components/PcPeripherals.vue";
import PeripheralParts from "@/components/PeripheralParts.vue";
import PcComponents from "@/components/PcComponents.vue";
import BottomContent from "@/components/BottomContent.vue";
import { ChevronDown } from 'lucide-vue-next';
import PDFGeneration from '@/views/PDFGeneration.vue';

const pdfGen = ref(null);
const subtotal = ref(0);
const activeTab = ref('Components');
const isUsd = ref(false)

const state = ref({
  components: {
    selectionItem: {},
    selectedParts: {},
    selectedPrices: {}
  },
  peripherals: {
    selectionItem: {},
    selectedParts: {},
    selectedPrices: {}
  }
});

const currentTabState = computed(() => {
  return activeTab.value === 'Components'
    ? state.value.components
    : state.value.peripherals;
});

// PcComponents
const selectedItem = (data) => {
  return currentTabState.value.selectionItem.slug = data.selectedItem;
};

// ComputerParts get Emits
const updateSelectedParts = (part) => {
  currentTabState.value.selectedParts[part.selectedComponent] = {
    id: part.selectedPartId,
    name: part.selectedComponentsPart,
    img: part.selectedComponentsPartImg,
    priceUsd: part.priceUsd,
    pricePhp: part.pricePhp,
    socket: part.socket,
    compatibleCpuSockets: part.compatibleCpuSockets
  }
}

const getPartsPriceData = (data) => {
  currentTabState.value.selectedPrices[data.component] = {
    priceUsd: data.priceUsd,
    pricePhp: data.pricePhp
  }
  return currentTabState.value.selectedPrices;
}

const handleRemoveSelectedParts = ({ slugToRemove }) => {
  const newSelectedParts = { ...currentTabState.value.selectedParts };
  delete newSelectedParts[slugToRemove];
  currentTabState.value.selectedParts = newSelectedParts;

  const newSelectedPrices = { ...currentTabState.value.selectedPrices };
  delete newSelectedPrices[slugToRemove];
  currentTabState.value.selectedPrices = newSelectedPrices;

  // subtotal.value -= priceToRemove;
  subtotal.value = Object.values({
    ...state.value.components.selectedPrices,
    ...state.value.peripherals.selectedPrices
  }).reduce((sum, item) => sum + (isUsd.value ? item.priceUsd : item.pricePhp), 0);
}

const handleSave = () => {
  localStorage.setItem("pcBuilderData", JSON.stringify(state.value));
  pdfGen.value.generatePDF();
}

const tabs = [
  { name: 'Components', href: '#'},
  { name: 'Peripherals', href: '#'},
]

const changeTab = (tabName) => {
  activeTab.value = tabName;
};

watch(isUsd, (newVal) => {
    console.log('Passed:', newVal)
  },{
    immediate: true
  }
);


</script>

<template>
  <main class="relative md:w-screen md:h-screen">
    <div class="w-full h-full">
      <header class="flex items-center justify-between">
        <div class="grid grid-cols-1 sm:hidden">
          <select aria-label="Select a tab"
          class="col-start-1 row-start-1 w-full appearance-none rounded-md bg-white py-2 pr-8 pl-3 text-base text-gray-900 outline-1 -outline-offset-1 outline-gray-300 focus:outline-2 focus:-outline-offset-2 focus:outline-indigo-600 dark:bg-white/5 dark:text-gray-100 dark:outline-white/10 dark:*:bg-gray-800 dark:focus:outline-indigo-500"
          v-model="activeTab">
            <option
            v-for="tab in tabs"
            :key="tab.name"
            :selected="tab.name === activeTab">{{ tab.name }}</option>
          </select>
          <ChevronDown class="pointer-events-none col-start-1 row-start-1 mr-2 size-5 self-center justify-self-end fill-gray-500 dark:fill-gray-400" aria-hidden="true" />
        </div>
        <div class="hidden sm:block p-6">
          <div class="border-b border-gray-200 dark:border-white/10">
            <nav class="-mb-px flex space-x-8" aria-label="Tabs">
              <a v-for="tab in tabs" :key="tab.name" :href="tab.href"
              :class="[
                tab.name === activeTab
                  ? 'border-indigo-500 text-indigo-600 dark:border-indigo-400 dark:text-indigo-400'
                  : 'border-transparent text-gray-500 hover:border-gray-300 hover:text-gray-700 dark:text-gray-400 dark:hover:border-white/20 dark:hover:text-gray-200',
                'border-b-2 px-1 py-4 text-sm font-medium whitespace-nowrap'
              ]"
              @click.prevent="changeTab(tab.name)">{{ tab.name }}</a>
            </nav>
          </div>
        </div>

        <div
        class="flex gap-1.5 items-center p-4">
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
            v-model="isUsd" />
          </div>
          <label
          for="toggleCurr"
          :class="isUsd ? 'text-green-500' : 'text-gray-500'"
          >USD</label>
        </div>
      </header>
      <!-- <p class="text-white">Active Tab: {{ activeTab }}</p> -->

      <div
        v-if="activeTab === 'Components'"
        class="flex w-full h-full p-6 md:flex-row flex-col gap-y-12">
        <PcComponents
          @update-component-selection="selectedItem"
          @remove-selected-parts="handleRemoveSelectedParts"
          :selected-parts="state.components.selectedParts"
          :is-currency-usd="isUsd" />

        <ComputerPartsSection
          :selected-item="state.components.selectionItem"
          :selected-parts="state.components.selectedParts"
          :is-currency-usd="isUsd"
          @selected-parts="updateSelectedParts"
          @price-of-selected="getPartsPriceData"
        />
      </div>

      <div
        v-else-if="activeTab === 'Peripherals'"
        class="flex w-full h-full p-6 md:flex-row flex-col gap-y-12">
        <PcPeripherals
          @update-peripheral-selection="selectedItem"
          @remove-selected-parts="handleRemoveSelectedParts"
          :selected-parts="state.peripherals.selectedParts"
          :is-currency-usd="isUsd"
        />

        <PeripheralParts
          @selected-parts="updateSelectedParts"
          @price-of-selected="getPartsPriceData"
          :selected-item="state.peripherals.selectionItem"
          :selected-parts="state.peripherals.selectedParts"
          :is-currency-usd="isUsd"
        />
      </div>

    </div>

    <BottomContent
      :selected-price="{
        ...state.components.selectedPrices,
        ...state.peripherals.selectedPrices
      }"
      :is-currency-usd="isUsd"
      :subtotal="subtotal"
      @save="handleSave"
    />
    <PDFGeneration ref="pdfGen" />
  </main>
</template>
