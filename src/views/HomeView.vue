<script setup>
import { ref } from "vue";
import ComputerPartsSection from "@/components/ComputerParts.vue";
import PcComponents from "@/components/PcComponents.vue";
import BottomContent from "@/components/BottomContent.vue";

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
const handleRemoveComponent = (slug) => {
  selectedPartsData.value = {
    ...selectedPartsData.value,
    [slug]: null,
  }
}

</script>

<template>
  <main class="relative w-screen h-screen">
    <div class="flex w-full h-full p-6">
      <PcComponents
        @update-component-selection="selectedComponent"
        @remove-component="handleRemoveComponent"
        :selected-parts="selectedPartsData" />

      <ComputerPartsSection
        :selected-item="pcComponentsData"
        :selected-parts="selectedPartsData"
        @selected-parts="updateSelectedParts"
       />
    </div>
    <BottomContent />
  </main>
</template>
