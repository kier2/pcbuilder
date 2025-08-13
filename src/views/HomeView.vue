<script setup>
import { ref } from "vue";
import ComputerPartsSection from "@/components/ComputerParts.vue";
import PcComponents from "@/components/PcComponents.vue";
import BottomContent from "@/components/BottomContent.vue";

const pcComponentsData = ref({});
const selectedPartsData = ref({});
const selectedPartsPriceData = ref({});
const selectedCurrencyData = ref();
const subtotal = ref(0);

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
    pricePhp: part.pricePhp
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

</script>

<template>
  <main class="relative md:w-screen md:h-screen">
    <div class="flex w-full h-full p-6 md:flex-row flex-col gap-y-12">
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
    <BottomContent
      :selected-price="selectedPartsPriceData"
      :is-currency-usd="selectedCurrencyData"
      :subtotal="subtotal"
    />
  </main>
</template>
