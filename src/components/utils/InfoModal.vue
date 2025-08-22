<script setup>
import { Dialog, DialogPanel, DialogTitle, TransitionChild, TransitionRoot } from '@headlessui/vue'

// Props from parent
const props = defineProps({
  show: Boolean,
  info: {
    type: Object,
    required: true
  }
})

// Emit close event back to parent
const emit = defineEmits(['close'])
</script>

<template>
  <TransitionRoot as="template" :show="show">
    <Dialog class="relative z-10" @close="emit('close')">
      <!-- Background overlay -->
      <TransitionChild as="template"
        enter="ease-out duration-300"
        enter-from="opacity-0"
        enter-to="opacity-100"
        leave="ease-in duration-200"
        leave-from="opacity-100"
        leave-to="opacity-0">
        <div class="fixed inset-0 bg-gray-700/75 transition-opacity" />
      </TransitionChild>

      <!-- Modal content -->
      <div class="fixed inset-0 z-10 w-screen overflow-y-auto">
        <div class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0">
          <TransitionChild as="template"
            enter="ease-out duration-300"
            enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
            enter-to="opacity-100 translate-y-0 sm:scale-100"
            leave="ease-in duration-200"
            leave-from="opacity-100 translate-y-0 sm:scale-100"
            leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95">
            <DialogPanel class="relative transform overflow-hidden rounded-lg bg-gray-900 px-4 pt-5 pb-4 text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-3xl sm:p-6">

              <!-- Close button -->
              <button
                class="text-white absolute top-3 right-3 z-10 cursor-pointer"
                type="button"
                @click="emit('close')">
                <span class="text-4xl">&times;</span>
              </button>

              <!-- Content -->
              <div>
                <div class="mx-auto flex size-40 items-center justify-center">
                  <img :src="props.info.img" alt="">
                </div>
                <div class="mt-3 text-center sm:mt-5 text-white">
                  <DialogTitle as="h3" class="text-xl font-semibold">
                    {{ props.info.name }}
                  </DialogTitle>

                  <div class="mt-5">
                    <table class="relative min-w-full">
                      <thead>
                        <tr>
                          <th class="py-3.5 pr-3 pl-4 text-left text-sm font-semibold text-white sm:pl-0">
                            <span class="sr-only">Name</span>
                          </th>
                          <th class="px-3 py-3.5 text-left text-sm font-semibold text-white">
                            <span class="sr-only">Specs</span>
                          </th>
                        </tr>
                      </thead>
                      <tbody class="divide-y divide-white/10">
                        <tr v-for="(spec, index) in props.info.specs" :key="index">
                          <td class="py-4 pr-3 pl-4 text-sm font-medium whitespace-nowrap text-gray-400 sm:pl-0 text-left">
                            {{ spec.label }}
                          </td>
                          <td class="pl-6 px-3 py-4 text-sm text-white text-left">
                            {{ spec.value }}
                          </td>
                        </tr>
                      </tbody>
                    </table>
                  </div>
                </div>
              </div>

              <!-- Footer -->
              <div class="mt-5 sm:mt-6 flex justify-end">
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
