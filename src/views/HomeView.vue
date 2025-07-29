<script setup>
import { ref } from "vue";
import ComputerPartsSection from "@/components/ComputerParts.vue";
import SelectedComponents from "@/components/PcComponents.vue";

const pcComponentsData = ref({});
const selectedPartsData = ref({});

const selectedComponent = (pcComponent) => {
  return pcComponentsData.value.slug = pcComponent.selectedItem;
};

const updateSelectedParts = (part) => {
  // console.log(part.selectedPartId)
  selectedPartsData.value[part.selectedComponent] = {
    id: part.selectedPartId,
    name: part.selectedComponentsPart,
    img: part.selectedComponentsPartImg,
  }


}

</script>

<template>
  <main class="w-screen h-screen">
    <div class="flex w-full h-full p-6">
      <SelectedComponents
        @update-component-selection="selectedComponent"
        :selected-parts="selectedPartsData" />

      <ComputerPartsSection
        :selected-item="pcComponentsData"
        :selected-parts="selectedPartsData"
        @selected-parts="updateSelectedParts"
       />
    </div>
  </main>
</template>
