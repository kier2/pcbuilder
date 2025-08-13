<script setup>
  import { watch, computed } from 'vue';
  import { ArrowUpFromLine, FileText, PhilippinePeso, DollarSign } from 'lucide-vue-next';


   // props
  const props = defineProps({
    selectedPrice: {
      type: Object,
      default: () => ({})
    },
    subtotal: Number,
    isCurrencyUsd: Boolean
  });


  const getSubtotal = computed(() => {
    let totalUsd = 0
    let totalPhp = 0

    for (const componentType in props.selectedPrice) {
    const part = props.selectedPrice[componentType];

      if (part && typeof part === 'object') {
        totalUsd += part.priceUsd || 0;
        totalPhp += part.pricePhp || 0;
      }
    }

    return {
      totalUsd: totalUsd,
      totalPhp: totalPhp
    }
  })

  const formatNumber = (value) => {
    if (!value) return 0;
    return new Intl.NumberFormat('en-US').format(value);
  };

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
          <div class="text-xl font-semibold flex">
            <span class="mr-3 text-gray-200"> Subtotal: </span>
            <div
            v-if="isCurrencyUsd"
            class="flex items-center gap-2">
              <DollarSign class="text-gray-400 size-4" />
              <span>{{ formatNumber(getSubtotal.totalUsd) }} </span>
            </div>
            <div
            v-else
            class="flex items-center gap-2">
              <PhilippinePeso class="text-gray-400 size-4" />
              <span>{{ formatNumber(getSubtotal.totalPhp) }} </span>
            </div>
          </div>
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

