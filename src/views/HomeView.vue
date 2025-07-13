<script setup>
import { ref } from "vue";
import ComputerPartsSection from "@/components/ComputerParts.vue";
import SelectedComponents from "@/components/PcComponents.vue";

const pcComponentsData = ref({});

const selectedComponent = (pcComponent) => {
  return pcComponentsData.value.item = pcComponent.selectedItem;
};
</script>

<template>
  <main class="w-screen h-screen">
    <div class="flex w-full h-full p-6">
      <SelectedComponents @update-component-selection="selectedComponent" />
      <ComputerPartsSection :selected-item="pcComponentsData" />
    </div>
  </main>
</template>
