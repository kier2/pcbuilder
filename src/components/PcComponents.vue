<script setup>
import { ref } from "vue";
import {
  Microchip,
  Cpu,
  Fan,
  MemoryStick,
  Monitor,
  Gpu,
  PcCase,
  HardDrive,
} from "lucide-vue-next";

const pcComponents = ref([
  {
    name: "Motherboard",
    slug: "motherboards",
    icon: Microchip,
    selectedItem: "Select Item",
    price: "",
  },
  {
    name: "Processor",
    slug: "cpu",
    icon: Cpu,
    selectedItem:
      "AMD Ryzen 5 5600 Socket AM4 3.5GHz Processor with Wraith Stealth Cooler MPK",
    price: "4,895.00",
  },
  {
    name: "CPU Cooler",
    slug: "fan",
    icon: Fan,
    selectedItem: "Select Item",
    price: "",
  },
  {
    name: "Memory",
    slug: "ram",
    icon: MemoryStick,
    selectedItem: "Select Item",
    price: "",
  },
  {
    name: "Storage",
    slug: "storage",
    icon: HardDrive,
    selectedItem: "Select Item",
    price: "",
  },
  {
    name: "Graphics Card",
    slug: "gpu",
    icon: Gpu,
    selectedItem: "Select Item",
    price: "",
  },
  {
    name: "Power Supply",
    slug: "psu",
    icon: Gpu,
    selectedItem: "Select Item",
    price: "",
  },
  {
    name: "Case",
    slug: "case",
    icon: PcCase,
    selectedItem: "Select Item",
    price: "",
  },
  {
    name: "Monitor",
    slug: "monitor",
    icon: Monitor,
    selectedItem: "Select Item",
    price: "",
  },
]);

const emit = defineEmits(["updateComponentSelection"]);

const handleClick = (e) => {
  const targetComponentDiv = e.target.closest("div[data-component]");
  if (targetComponentDiv) {
    emit("updateComponentSelection", {
      selectedItem: targetComponentDiv.dataset.component,
    });
  }
};
</script>
<template>
  <div class="overflow-hidden shadow sm:rounded-md w-full">
    <ul role="list" class="text-gray-300 flex flex-col space-y-3">
      <li
        v-for="(pcComponent, index) in pcComponents"
        :key="index"
        @click="handleClick"
        class="bg-gray-900/80 shadow-sm rounded transition cursor-pointer hover:bg-gray-900/90 border border-gray-900/80 hover:border-gray-50"
      >
        <div
          class="flex items-center gap-6 px-4 py-8 sm:px-6 component"
          :data-component="pcComponent.slug"
        >
          <component :is="pcComponent.icon"></component>
          <h4 class="flex-auto truncate text-xl font-semibold">
            {{ pcComponent.name }}
          </h4>
        </div>
      </li>
    </ul>
  </div>
</template>
