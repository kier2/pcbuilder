<script setup>
import { Dialog, DialogPanel, TransitionChild, TransitionRoot } from '@headlessui/vue'

// Props from parent
const props = defineProps({
  show: Boolean,
  items: {
    type: Array,
    required: true
  }
})

// Emit close event back to parent
const emit = defineEmits(['close'])
</script>

<template>
  <TransitionRoot as="template" :show="show">
    <Dialog class="relative z-10" @close="emit('close')">
      <TransitionChild as="template"
        enter="ease-out duration-300"
        enter-from="opacity-0"
        enter-to="opacity-100"
        leave="ease-in duration-200"
        leave-from="opacity-100"
        leave-to="opacity-0">
        <div class="fixed inset-0 bg-gray-700/75 transition-opacity" />
      </TransitionChild>

      <div class="fixed inset-0 z-10 w-screen overflow-y-auto">
        <div class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0">
          <TransitionChild as="template"
            enter="ease-out duration-300"
            enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
            enter-to="opacity-100 translate-y-0 sm:scale-100"
            leave="ease-in duration-200"
            leave-from="opacity-100 translate-y-0 sm:scale-100"
            leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95">
            <DialogPanel class="relative transform overflow-hidden rounded-lg bg-gray-900 shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-3xl">

              <div class="px-6 pt-16 pb-0">
                <button
                  class="text-white absolute top-3 right-3 z-10 cursor-pointer"
                  type="button"
                  @click="emit('close')">
                  <span class="text-4xl">&times;</span>
                </button>

                <!-- Loop over items from parent -->
                <div v-for="(item, index) in props.items" :key="index" class="bg-gray-800 rounded-lg p-4 mb-4 flex justify-between items-start">
                  <div class="flex-grow text-white text-left">
                    <p class="font-bold text-xl mb-1">{{ item.name }}</p>
                    <p class="text-gray-400">{{ item.description }}</p>
                  </div>
                  <div class="text-right text-white ml-6">
                    <p class="font-bold text-lg">PRICE: {{ item.price }}</p>
                  </div>
                </div>

                <!-- <div class="mt-4">
                  <button type="button"
                    class="w-full bg-red-600 text-white font-semibold py-3 rounded-lg shadow-md transition-all hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-opacity-50">
                    <span class="inline-flex items-center">
                      <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
                        <path d="M10 2a8 8 0 100 16 8 8 0 000-16zM5 10a1 1 0 011-1h3V6a1 1 0 112 0v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 01-1-1z" />
                      </svg>
                      Share to Reddit
                    </span>
                  </button>
                </div> -->
              </div>
              <!-- Footer -->
              <div class="mt-5 sm:mt-6 flex justify-end p-6">
                <button type="button"
                  class="inline-flex justify-center rounded px-3 py-2 text-sm font-semibold text-white shadow-xs sm:col-start-2 ring-2
                  hover:bg-white hover:text-gray-900/80 cursor-pointer"
                  @click="emit('close')">Close</button>
              </div>
            </DialogPanel>
          </TransitionChild>
        </div>
      </div>
    </Dialog>
  </TransitionRoot>
</template>
