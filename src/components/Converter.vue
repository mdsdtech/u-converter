<template>
  <div class="max-w-2xl mx-auto p-6 bg-white shadow-xl rounded-2xl mt-12">
    <h1 class="text-3xl font-bold text-center mb-6">VMess ➜ VLESS Converter</h1>

    <textarea
      v-model="vmessInput"
      placeholder="Paste VMess config here (vmess://...)"
      class="w-full p-4 border rounded-xl focus:outline-none focus:ring focus:ring-blue-300 resize-none h-40"
    ></textarea>

    <button
      @click="convertToVLESS"
      class="mt-4 w-full bg-blue-600 text-white py-3 rounded-xl hover:bg-blue-700 transition"
    >
      Convert
    </button>

    <div v-if="vlessOutput" class="mt-6 bg-gray-100 p-4 rounded-xl break-all">
      <strong>VLESS Link:</strong>
      <p class="mt-2 text-blue-700">{{ vlessOutput }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const vmessInput = ref('');
const vlessOutput = ref('');

const convertToVLESS = () => {
  try {
    const input = vmessInput.value.trim();
    const base64Data = input.startsWith('vmess://') ? input.slice(8) : input;
    const jsonStr = atob(base64Data);
    const config = JSON.parse(jsonStr);

    const vless = `vless://${config.id}@${config.add}:${config.port}?encryption=none&security=${config.tls ?? 'none'}&type=${config.net}&host=${config.host}&path=${config.path}#${encodeURIComponent(config.ps)}`;
    vlessOutput.value = vless;
  } catch {
    vlessOutput.value = '⚠️ Invalid VMess config';
  }
};
</script>
