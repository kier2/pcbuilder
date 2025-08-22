<script setup>
import { ref, computed } from "vue";
import html2pdf from "html2pdf.js";

// Props from parent
const props = defineProps({
  data: {
    type: Object,
    required: true
  },
  isCurrencyUsd: {
    type: Boolean,
    default: false
  }
});

const pdfContent = ref(null);

const today = new Date().toLocaleDateString();

const selectedCurrency = computed(() => props.isCurrencyUsd ? "USD" : "PHP");

const total = computed(() => {
  let sum = 0;

  const currencyKey = selectedCurrency.value === "USD" ? "priceUsd" : "pricePhp";

  Object.values(props.data.components?.selectedParts || {}).forEach(
    (p) => (sum += p[currencyKey] || 0)
  );
  Object.values(props.data.peripherals?.selectedParts || {}).forEach(
    (p) => (sum += p[currencyKey] || 0)
  );
  return sum;
});

// Generate PDF in new tab
const generatePDF = () => {
  const element = pdfContent.value;
  const opt = {
    margin: 10,
    filename: "quotation.pdf",
    html2canvas: { scale: 2 },
    jsPDF: { unit: "mm", format: "a4", orientation: "portrait" }
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

const formatCurrency = (value) => {
  return new Intl.NumberFormat("en-US", {
    style: "currency",
    currency: selectedCurrency.value,
    minimumFractionDigits: 2
  }).format(value);
};

defineExpose({ generatePDF });
</script>

<template>
  <div ref="pdfContent" class="quotation">
    <div class="header">
      <h1>
        <span style="color: #030712; font-weight: bold;">Pc</span>
        <span style="color: #49d763; font-weight: bold;">Build</span>
      </h1>
    </div>

    <div class="details">
      <div><span>Date:</span> {{ today }}</div>
    </div>

    <h2>Components</h2>
    <table>
      <thead>
        <tr>
          <th>Item</th>
          <th>Price ({{ selectedCurrency }})</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, key) in props.data.components?.selectedParts" :key="key">
          <td>{{ item.name }}</td>
          <td>
            {{ formatCurrency(
              props.selectedCurrency === "USD" ? item.priceUsd : item.pricePhp,
              props.selectedCurrency
            ) }}
          </td>
        </tr>
      </tbody>
    </table>

    <h2>Peripherals</h2>
    <table>
      <thead>
        <tr>
          <th>Item</th>
          <th>Price ({{ selectedCurrency }})</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, key) in props.data.peripherals?.selectedParts" :key="key">
          <td>{{ item.name }}</td>
          <td>
            {{ formatCurrency(
              props.selectedCurrency === "USD" ? item.priceUsd : item.pricePhp,
              props.selectedCurrency
            ) }}
          </td>
        </tr>
      </tbody>
    </table>

    <div class="totals">
      <span>Total ({{ selectedCurrency }}):</span>
      {{ formatCurrency(total, props.selectedCurrency) }}
    </div>
  </div>
</template>
