<script setup>
  import { ref, onMounted, computed } from "vue";

  // props
  const props = defineProps({
    selectedItem: Object,
    selectedParts: Object
  });

  // emits
  const  emit = defineEmits(['selectedParts']);

  // variables
  const allComponentData = ref({});
  const loading = ref(true);
  const error = ref(null);

  // Database Actions
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

  // Events
  const handleSelectedParts = (id, name, img ) => {
    emit('selectedParts', {
      selectedPartId: id,
      selectedComponent: props.selectedItem.slug,
      selectedComponentsPart: name,
      selectedComponentsPartImg: img,
    })
  }

  onMounted(() => {
    fetchData();
  })

  const currentParts = computed(() => {
    if(loading.value || error.value || !props.selectedItem || !props.selectedItem.slug){
      return [];
    }
    const parts = allComponentData.value[props.selectedItem.slug];
    // console.log(parts)
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
          class="bg-gray-700/80 rounded text-white hover:ring-2 hover:ring-indigo-300">
            <!-- <div class="hidden group-hover:block absolute">
              <button
              class="text-red-300"
              type="button">
                Clear
              </button>
            </div> -->
            <div class="p-8 space-y-6">
              <div>
                <p>{{ part.name }}</p>
              </div>
              <div class="w-full flex items-center justify-between">
                <button class="border border-indigo-400 cursor-pointer hover:bg-indigo-400 px-3 py-1.5 text-sm rounded" type="button">Info</button>
                <button
                  class="border border-indigo-400 cursor-pointer hover:bg-indigo-400 px-3 py-1.5 text-sm rounded"
                  :class="props.selectedParts?.[props.selectedItem?.slug]?.id === part.id ? 'bg-indigo-400' : ''"
                  type="button"
                  @click="handleSelectedParts(part.id, part.name, part.img)">
                    {{ props.selectedParts?.[props.selectedItem?.slug]?.id === part.id ? 'Selected' : 'Select' }}
                </button>
              </div>
            </div>
        </li>
      </ul>
    </div>
  </div>
</template>
