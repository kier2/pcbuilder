<script setup>
  import { ref, onMounted, computed } from "vue";

  const props = defineProps({
    selectedItem: Object,
  });

  const allComponentData = ref({});
  const loading = ref(true);
  const error = ref(null);

  const Motherboard = ref([
    {
      model: "GA-H110M-S2H",
      brand: "Gigabyte",
      img: '',
      price: ''
    },
    {
      model: "H110M-E/M.2",
      brand: "Asus",
      img: '',
      price: ''
    },
  ]);

  const fetchData = async () => {
    try{
      const res = await fetch('/data/data.json');
      if(!res.ok){
        throw new Error(`HTTP error! status: ${res.status}`);
      }

      const data = await res.json();
      allComponentData.value = data
    } catch (err) {
      error.value = 'Failed to load data.';
      console.error("Could not fetch data", err);
    } finally {
      loading.value = false;
    }
  }

  onMounted(() => {
    fetchData();
  })

  const currentParts = computed(() => {
    if(loading.value || error.value || !props.selectedItem || !props.selectedItem.slug){
      return [];
    }
    const parts = allComponentData.value[props.selectedItem.slug];
    console.log(parts)
    return parts || [];
  });
</script>

<template>
  <div class="w-full">
    <div class="pl-10 pr-3 py-2">
      <h2 class="text-white uppercase text-2xl font-semibold mb-5">
        <span v-if="props.selectedItem">
        {{ props.selectedItem.slug }}
      </span>
      </h2>

      <p v-if="loading">Loading parts...</p>
      <p v-else-if="error" style="color: red;">{{ error }}</p>

      <ul
        v-else-if="currentParts.length > 0"
        class="space-y-3">
        <li
          v-for="(part, index) in currentParts"
          :key="index"
          class="bg-gray-700/80 rounded text-white">
            <div class="p-6 space-y-6">
              <div>
                <p>{{ part.name }}</p>
              </div>
              <div class="w-full flex items-center justify-between">
                <button class="border border-indigo-400 cursor-pointer hover:bg-indigo-400 px-3 py-1.5 text-xs rounded" type="button">Info</button>
                <button class="border border-indigo-400 cursor-pointer hover:bg-indigo-400 px-3 py-1.5 text-xs rounded" type="button">Select</button>
              </div>
            </div>
        </li>
      </ul>
    </div>
  </div>
</template>
