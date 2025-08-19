<script setup>
import { ref, watch } from "vue";
import {
  Monitor,
  Trash2,
  Keyboard,
  Mouse,
  Headphones
} from "lucide-vue-next";

// This is the array of peripherals
const peripherals = ref([
  {
    name: "Keyboard",
    slug: "keyboard",
    icon: Keyboard,
  },
  {
    name: "Mouse",
    slug: "mouse",
    icon: Mouse,
  },
  {
    name: "Headset",
    slug: "headset",
    icon: Headphones,
  },
  {
    name: "Monitor",
    slug: "monitor",
    icon: Monitor,
  }
]);

// variables
const selectedPeripherals = ref();
const activePeripheralSlug = ref(null);

// props
const props = defineProps({
  selectedParts: {
    type: Object,
    default: () => ({})
  },
  isCurrencyUsd: Boolean
})

// emits
const emit = defineEmits(["updatePeripheralSelection", "removePeripheral"]);

// Functions
const getSelectedPartName = (slug) => {
  const part = props.selectedParts?.[slug];
  return part?.name ?? null;
}

const handleClick = (e) => {
  const targetPeripheralDiv = e.target.closest("div[data-peripheral]");
  if (targetPeripheralDiv) {
    const slug = targetPeripheralDiv.dataset.peripheral;
    selectedPeripherals.value = slug;
    activePeripheralSlug.value = slug;
    emit("updatePeripheralSelection", {
      selectedItem: slug,
    });
  }
};

const handleRemovePeripheral = (slugToRemove) => {
  const partToRemove = props.selectedParts?.[slugToRemove];

  if (partToRemove) {
    const priceToRemove = props.isCurrencyUsd ? partToRemove.priceUsd : partToRemove.pricePhp;
    emit("removePeripheral", { slugToRemove, priceToRemove });
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
      role="list"
      class="text-gray-300 flex flex-col space-y-3"
    >
      <li
        v-for="(peripheral, index) in peripherals"
        :key="index"
        @click="handleClick"
        class="bg-gray-900/80 shadow-sm rounded cursor-pointer group transition-all duration-300 ease-in-out"
        :class="{
          // Border for when a part has been selected
          'border-l-6 border-indigo-400': getSelectedPartName(peripheral.slug),
          // Default border when no part is selected
          'border-l-6 border-gray-900/80': !getSelectedPartName(peripheral.slug),
          // Ring for the currently clicked item
          'border-l-6 border-r-0 border-t-0 border-b-0 border-indigo-400': peripheral.slug === activePeripheralSlug
        }"
      >
        <div
          v-if="getSelectedPartName(peripheral.slug)"
          class="absolute right-2 top-2"
        >
          <button
            @click.stop="handleRemovePeripheral(peripheral.slug)"
            class="text-red-300 cursor-pointer"
            type="button"
          >
            <Trash2 class="size-4" />
          </button>
        </div>
        <div
          class="flex items-center gap-6 px-4 py-8 sm:px-6"
          :data-peripheral="peripheral.slug"
        >
          <component :is="peripheral.icon" class="text-gray-400"></component>
          <div>
            <h4 class="flex-auto truncate text-xl font-semibold mb-2 text-gray-400">
              {{ peripheral.name }}
            </h4>
            <p class="text-sm text-gray-200">
              {{ getSelectedPartName(peripheral.slug) || 'Select Item' }}
            </p>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>
