<script setup>
import { ref, watch } from "vue";
import {
  Microchip,
  Cpu,
  Fan,
  MemoryStick,
  Gpu,
  PcCase,
  HardDrive,
  Trash2,
} from "lucide-vue-next";

const pcComponents = ref([
  {
    name: "Processor",
    slug: "cpu",
    icon: Cpu,
  },
  {
    name: "Motherboard",
    slug: "motherboard",
    icon: Microchip,
  },
  {
    name: "CPU Cooler",
    slug: "cooler",
    icon: Fan,
  },
  {
    name: "Memory",
    slug: "ram",
    icon: MemoryStick,
  },
  {
    name: "Storage",
    slug: "storage",
    icon: HardDrive,
  },
  {
    name: "Graphics Card",
    slug: "gpu",
    icon: Gpu,
  },
  {
    name: "Power Supply",
    slug: "psu",
    icon: Gpu,
  },
  {
    name: "Case",
    slug: "case",
    icon: PcCase,
  }
]);

// variables
const selectedComponent = ref();
// const selectedPartsData = ref({})
const activeComponentSlug = ref(null);

// props
const props = defineProps({
  selectedParts: {
    type: Object,
    default: () => ({})
  },
  isCurrencyUsd: Boolean
})

// emits
const emit = defineEmits(["updateComponentSelection", "removeComponent"]);

// Database Actions
// const fetchSelectedParts = () => {
//   if(Object.keys(props.selectedParts).length){
//     selectedPartsData.value.name = props.selectedParts.name
//   }
// }

const getSelectedPartName = (slug) =>  {
  const part = props.selectedParts?.[slug];
  return part?.name ?? null;
}

// Events
const handleClick = (e) => {
  const targetComponentDiv = e.target.closest("div[data-component]");
  if (targetComponentDiv) {
    const slug = targetComponentDiv.dataset.component;
    selectedComponent.value = targetComponentDiv.dataset.component
    activeComponentSlug.value = slug;
    emit("updateComponentSelection", {
      selectedItem: slug,
    });
  }
};

const handleRemoveComponent = (slugToRemove) => {
  // Get the part to be removed
  const partToRemove = props.selectedParts?.[slugToRemove];

  if (partToRemove) {
    const priceToRemove = props.isCurrencyUsd ? partToRemove.priceUsd : partToRemove.pricePhp;
    emit("removeComponent", { slugToRemove, priceToRemove });
  }
}

watch(
  [() => props.selectedParts, () => props.isCurrencyUsd],
  () => {
  },
  {
    deep: true,
  }
);

</script>
<template>
  <div class=" overflow-y-auto overflow-x-hidden shadow sm:rounded-md w-full md:h-[75%] md:sticky md:top-0 custom-scrollbar">
    <ul
     role="list" class="text-gray-300 flex flex-col space-y-3">
      <li
        v-for="(pcComponent, index) in pcComponents"
        :key="index"
        @click="handleClick"
        class="bg-gray-900/80 shadow-sm rounded cursor-pointer group transition-all duration-300 ease-in-out"
        :class="{
          // Border for when a part has been selected
          'border-l-6 border-indigo-400': getSelectedPartName(pcComponent.slug),
          // Default border when no part is selected
          'border-l-6 border-gray-900/80': !getSelectedPartName(pcComponent.slug),
          // Ring for the currently clicked item
          'border-l-6 border-r-0 border-t-0 border-b-0 border-indigo-400': pcComponent.slug === activeComponentSlug
        }"
      >
        <div
        v-if="getSelectedPartName(pcComponent.slug)"
        class="absolute right-2 top-2">
          <button
            @click.stop="handleRemoveComponent(pcComponent.slug)"
            class="text-red-300 cursor-pointer"
            type="button">
              <Trash2 class="size-4" />
          </button>
        </div>
        <div
          class="flex items-center gap-6 px-4 py-8 sm:px-6 component"
          :data-component="pcComponent.slug"
        >
          <component :is="pcComponent.icon" class="text-gray-400"></component>
          <div>
            <h4 class="flex-auto truncate text-xl font-semibold mb-2 text-gray-400">
              {{ pcComponent.name }}
            </h4>
            <p class="text-sm text-gray-200">
              {{ getSelectedPartName(pcComponent.slug) || 'Select Item' }}
            </p>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>
