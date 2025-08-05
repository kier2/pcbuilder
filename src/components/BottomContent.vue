<script setup>
  import { ArrowUpFromLine, FileText } from 'lucide-vue-next';
</script>

<template>
  <footer class="bg-gray-800 p-8 w-full absolute bottom-0 text-white">
    <div class="flex items-center justify-between gap-3 mx-auto px-3">
      <div class="flex-1">
      </div>
      <div class="flex-1 flex justify-between items-center">
        <div class="">
          <h2 class="text-xl font-semibold">Subtotal: </h2>
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

