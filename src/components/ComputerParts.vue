<script setup>
import { ref } from "vue";
const props = defineProps({
  selectedItem: Object,
});
const Motherboard = ref([
  {
    model: "GA-H110M-S2H",
    brand: "Gigabyte",
  },
  {
    model: "H110M-E/M.2",
    brand: "Asus",
  },
]);
</script>

<template>
  <div class="w-full">
    <div v-if="props.selectedItem.item == 'motherboard'">
      <h2 class="text-white">{{ props.selectedItem.item }}</h2>
    </div>
    <div v-else-if="props.selectedItem.item == 'cpu'">
      <h2 class="text-white">{{ props.selectedItem.item }}</h2>
    </div>
    <div v-else-if="props.selectedItem.item == 'fan'">
      <h2 class="text-white">{{ props.selectedItem.item }}</h2>
    </div>
    <div v-else-if="props.selectedItem.item == 'ram'">
      <h2 class="text-white">{{ props.selectedItem.item }}</h2>
    </div>
    <div v-else-if="props.selectedItem.item == 'storage'">
      <h2 class="text-white">{{ props.selectedItem.item }}</h2>
    </div>
    <div v-else-if="props.selectedItem.item == 'gpu'">
      <h2 class="text-white">{{ props.selectedItem.item }}</h2>
    </div>
    <div v-else-if="props.selectedItem.item == 'psu'">
      <h2 class="text-white">{{ props.selectedItem.item }}</h2>
    </div>
    <div v-else-if="props.selectedItem.item == 'case'">
      <h2 class="text-white">{{ props.selectedItem.item }}</h2>
    </div>
    <div v-else-if="props.selectedItem.item == 'monitor'">
      <h2 class="text-white">{{ props.selectedItem.item }}</h2>
    </div>
    <div v-else>
      <h2 class="text-white"></h2>
    </div>
  </div>
</template>
