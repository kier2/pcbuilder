<script setup>
  import { watch, computed } from 'vue';
  import { ArrowUpFromLine, FileText } from 'lucide-vue-next';

   // props
  const props = defineProps({
    selectedPrice: {
      type: Object,
      default: () => ({})
    }
  });


  const getSubtotal = computed(() => {
    let totalUsd = 0
    let totalPhp = 0

    for (const componentType in props.selectedPrice) {
    const parts = props.selectedPrice[componentType];

      // Iterate over the array of parts for each component type
      if (Array.isArray(parts)) {
        parts.forEach(part => {
          totalUsd += part.priceUsd || 0;
          totalPhp += part.pricePhp || 0;
        });
      }
    }



    return {
      totalUsd: totalUsd,
      totalPhp: totalPhp
    }
  })

  watch(
  () => props.selectedPrice,
  (newVal) => {

    console.log("selectedParts content:", JSON.parse(JSON.stringify(newVal)));
  },
  {
    deep: true,
    immediate: true
  }
);

</script>

<template>
  <footer class="bg-gray-800 p-8 w-full absolute bottom-0 text-white">
    <div class="flex items-center justify-between gap-3 mx-auto px-3">
      <div class="flex-1">
      </div>
      <div class="flex-1 flex justify-between items-center">
        <div class="">
          <h2 class="text-xl font-semibold">Subtotal: {{ getSubtotal.totalUsd }}</h2>
        </div>
        <div class="flex justify-between items-center gap-6">
          <button
           class="text-center text-green-400 hover:text-gray-400 cursor-pointer outline-0"
           type="button">
           <ArrowUpFromLine class="size-6 mx-auto"/>
            Save
          </button>
          <button
           class="text-center text-green-400 hover:text-gray-400 cursor-pointer outline-0"
           type="button">
            <FileText class="size-6 mx-auto" />
            Summary
          </button>
        </div>
      </div>
    </div>
  </footer>
</template>

