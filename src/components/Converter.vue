<!-- App.vue -->
<template>
  <div class="min-h-screen flex items-center justify-center bg-gradient-to-b from-blue-100 to-blue-200">
    <div class="bg-white rounded-2xl shadow-xl p-8 w-full max-w-3xl">
      <div class="flex justify-center mb-4">
        <button
          v-for="option in tabs"
          :key="option"
          :class="[
            'px-4 py-2 mx-1 rounded-full transition-all',
            tab === option ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-700'
          ]"
          @click="tab = option"
        >
          {{ option }}
        </button>
      </div>

      <h1 class="text-2xl font-bold text-center mb-6">
        {{ tab }} Converter
      </h1>

      <textarea
        v-model="input"
        rows="5"
        placeholder="Paste config here..."
        class="w-full border rounded-lg p-4 text-gray-700 mb-4"
      ></textarea>

      <button
        @click="convert"
        class="bg-blue-600 text-white px-6 py-3 rounded-lg w-full hover:bg-blue-700 transition mb-2"
      >
        Convert
      </button>
      <button
      @click="reset"
    class="bg-gray-600 text-white px-6 py-3 rounded-lg w-full hover:bg-red-700 transition"
      >
    Reset
  </button>
<!-- 
      <div v-if="output" class="mt-6 bg-gray-100 p-4 rounded-lg break-all">
        <strong>Output:</strong>
        <p class="mt-2">{{ output }}</p>
      </div> -->
      <div v-if="output" class="mt-4 bg-gray-100 p-4 rounded-lg break-all">
        <label class="block mb-2 font-bold">Converted Config:</label>
        <div class="relative">
          <textarea
            class="w-full h-32 rounded-md p-4 pr-20 bg-gray-50"
            readonly
            :value="output"
          ></textarea>
          <button
            class="absolute top-2 right-2 px-4 py-1 bg-green-500 text-white text-sm rounded hover:bg-green-600"
            @click="copyToClipboard"
          >
            Copy
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const tabs = ['VMess → VLESS', 'VLESS → VMess']
const tab = ref(tabs[0])
const input = ref('')
const output = ref('')

const convert = () => {
  try {
    if (tab.value === 'VMess → VLESS') {
      const json = JSON.parse(atob(input.value.replace('vmess://', '')))
      output.value = `vless://${json.id}@${json.add}:${json.port}?encryption=none&security=${json.tls}&type=${json.net}&host=${json.host}&path=${json.path}#${encodeURIComponent(json.ps)}`
    } else {
      // Reverse conversion
      const url = new URL(input.value)
      const vlessData = {
        v: '2',
        ps: decodeURIComponent(url.hash.slice(1)),
        add: url.hostname,
        port: url.port,
        id: url.username,
        aid: '0',
        net: url.searchParams.get('type'),
        type: 'none',
        host: url.searchParams.get('host') || '',
        path: url.searchParams.get('path') || '',
        tls: url.searchParams.get('security'),
      }
      output.value = 'vmess://' + btoa(JSON.stringify(vlessData))
    }
  } catch (e) {
    output.value = '❌ Invalid config'
  }
}
const reset = () => {
  input.value = ''
  output.value = ''
  tab.value = tabs[0]
  document.querySelector('textarea').focus()
}

const copyToClipboard = () => {
  navigator.clipboard.writeText(output.value).then(() => {
    alert('Copied to clipboard!')
  }).catch(err => {
    console.error('Failed to copy: ', err)
  })
}
</script>
