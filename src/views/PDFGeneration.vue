<script setup>
import { ref, computed, onMounted } from "vue";
import html2pdf from "html2pdf.js";

const pdfContent = ref(null);

const data = ref({
  components: { selectedParts: {} },
  peripherals: { selectedParts: {} },
});

const today = new Date().toLocaleDateString();

onMounted(() => {
  const stored = localStorage.getItem("pcBuilderData");
  if (stored) {
    data.value = JSON.parse(stored);
  }
});

const totalUsd = computed(() => {
  let sum = 0;
  Object.values(data.value.components.selectedParts).forEach((p) => (sum += p.priceUsd));
  Object.values(data.value.peripherals.selectedParts).forEach((p) => (sum += p.priceUsd));
  return sum;
});

const totalPhp = computed(() => {
  let sum = 0;
  Object.values(data.value.components.selectedParts).forEach((p) => (sum += p.pricePhp));
  Object.values(data.value.peripherals.selectedParts).forEach((p) => (sum += p.pricePhp));
  return sum;
});

// Generate PDF in new tab
const generatePDF = () => {
  const element = pdfContent.value;
  const opt = {
    margin: 10,
    filename: "quotation.pdf",
    html2canvas: { scale: 2 },
    jsPDF: { unit: "mm", format: "a4", orientation: "portrait" },
  };

  html2pdf()
    .set(opt)
    .from(element)
    .outputPdf("blob")
    .then((pdfBlob) => {
      const pdfUrl = URL.createObjectURL(pdfBlob);
      window.open(pdfUrl, "_blank");
    });
};

defineExpose({ generatePDF });
</script>
<template>
  <div ref="pdfContent" class="pdf-design">
    <div id="quotation" class="max-w-3xl mx-auto bg-white p-10 rounded-lg shadow-lg">
    <!-- Header -->
    <div class="text-center mb-6">
      <h1 class="text-4xl font-bold text-blue-600">Your Company Name</h1>
      <p class="text-gray-600">Your Address, City, Postal Code</p>
      <p class="text-gray-600">
        Email: your.email@example.com | Phone: (123) 456-7890
      </p>
    </div>

    <!-- Details -->
    <div class="grid grid-cols-2 gap-4 border-b pb-4 mb-6 text-gray-700">
      <div><span class="font-semibold text-blue-600">Quotation ID:</span> Q-001</div>
      <div><span class="font-semibold text-blue-600">Date:</span> August 21, 2025</div>
      <div><span class="font-semibold text-blue-600">Customer:</span> John Doe</div>
    </div>

    <!-- Components -->
    <h2 class="text-xl font-semibold text-blue-600 border-b pb-1 mb-4">Components</h2>
    <table class="w-full border-collapse mb-6">
      <thead>
        <tr class="bg-blue-50">
          <th class="text-left py-3 px-4 font-semibold text-gray-700">Item</th>
          <th class="text-left py-3 px-4 font-semibold text-gray-700">Price (USD)</th>
          <th class="text-left py-3 px-4 font-semibold text-gray-700">Price (PHP)</th>
        </tr>
      </thead>
      <tbody>
        <tr class="hover:bg-gray-50">
          <td class="py-3 px-4">Intel Core i9-14900K</td>
          <td class="py-3 px-4">$550</td>
          <td class="py-3 px-4">₱32,000</td>
        </tr>
        <tr class="hover:bg-gray-50">
          <td class="py-3 px-4">GIGABYTE Z790 AORUS ELITE AX</td>
          <td class="py-3 px-4">$250</td>
          <td class="py-3 px-4">₱15,000</td>
        </tr>
      </tbody>
    </table>

    <!-- Peripherals -->
    <h2 class="text-xl font-semibold text-blue-600 border-b pb-1 mb-4">Peripherals</h2>
    <table class="w-full border-collapse mb-6">
      <thead>
        <tr class="bg-blue-50">
          <th class="text-left py-3 px-4 font-semibold text-gray-700">Item</th>
          <th class="text-left py-3 px-4 font-semibold text-gray-700">Price (USD)</th>
          <th class="text-left py-3 px-4 font-semibold text-gray-700">Price (PHP)</th>
        </tr>
      </thead>
      <tbody>
        <tr class="hover:bg-gray-50">
          <td class="py-3 px-4">Acer Nitro XV272U</td>
          <td class="py-3 px-4">$280</td>
          <td class="py-3 px-4">₱16,500</td>
        </tr>
      </tbody>
    </table>

    <!-- Totals -->
    <div class="text-right border-t pt-4 mt-6">
      <p class="text-lg font-semibold text-gray-800">
        <span class="text-blue-600">Total (USD):</span> $1080
      </p>
      <p class="text-lg font-semibold text-gray-800">
        <span class="text-blue-600">Total (PHP):</span> ₱63,500
      </p>
    </div>
  </div>
  </div>
</template>


