<script setup>
import { ref } from "vue";
import ComputerPartsSection from "@/components/ComputerParts.vue";
import PcComponents from "@/components/PcComponents.vue";
import BottomContent from "@/components/BottomContent.vue";
import { ChevronDown } from 'lucide-vue-next';

const pcComponentsData = ref({});
const selectedPartsData = ref({});
const selectedPartsPriceData = ref({});
const selectedCurrencyData = ref();
const subtotal = ref(0);
const activeTab = ref('Components');

const selectedComponent = (pcComponent) => {
  return pcComponentsData.value.slug = pcComponent.selectedItem;
};

// ComputerParts Get Emits
const updateSelectedParts = (part) => {
  // console.log(part.selectedPartId)
  selectedPartsData.value[part.selectedComponent] = {
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
  selectedPartsPriceData.value[data.component] = {
    priceUsd: data.priceUsd,
    pricePhp: data.pricePhp
  }
  return selectedPartsPriceData;
}

const getSelectedCurrency = (data) => {
  // console.log(data)
  selectedCurrencyData.value = data
}

const handleRemoveComponent = ({ slugToRemove, priceToRemove }) => {
  const newSelectedParts = { ...selectedPartsData.value };
  delete newSelectedParts[slugToRemove];
  selectedPartsData.value = newSelectedParts;

  const newSelectedPrices = { ...selectedPartsPriceData.value };
  delete newSelectedPrices[slugToRemove];
  selectedPartsPriceData.value = newSelectedPrices;

  subtotal.value -= priceToRemove;
}

const tabs = [
  { name: 'Components', href: '#'},
  { name: 'Peripherals', href: '#'},
]

const changeTab = (tabName) => {
  activeTab.value = tabName;
};

</script>

<template>
  <main class="relative md:w-screen md:h-screen">
    <div class="w-full h-full">
      <div class="grid grid-cols-1 sm:hidden">

        <select aria-label="Select a tab"
        class="col-start-1 row-start-1 w-full appearance-none rounded-md bg-white py-2 pr-8 pl-3 text-base text-gray-900 outline-1 -outline-offset-1 outline-gray-300 focus:outline-2 focus:-outline-offset-2 focus:outline-indigo-600 dark:bg-white/5 dark:text-gray-100 dark:outline-white/10 dark:*:bg-gray-800 dark:focus:outline-indigo-500"
        @change="changeTab($event.target.value)">
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
        v-if="activeTab === 'Components'"
      class="flex w-full h-full p-6 md:flex-row flex-col gap-y-12">
        <PcComponents
          @update-component-selection="selectedComponent"
          @remove-component="handleRemoveComponent"
          :selected-parts="selectedPartsData"
          :is-currency-usd="selectedCurrencyData" />

        <ComputerPartsSection
          :selected-item="pcComponentsData"
          :selected-parts="selectedPartsData"
          @selected-parts="updateSelectedParts"
          @price-of-selected="getPartsPriceData"
          @is-curr-usd="getSelectedCurrency"
        />
      </div>

      <div
        v-else
        class="flex w-full h-full p-6 md:flex-row flex-col gap-y-12">
      </div>

    </div>

    <BottomContent
      :selected-price="selectedPartsPriceData"
      :is-currency-usd="selectedCurrencyData"
      :subtotal="subtotal"
    />
  </main>
</template>
