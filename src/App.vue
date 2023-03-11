<script setup lang="ts">
import { ref } from 'vue';
import HelloWorld from './components/HelloWorld.vue'

const showApiKey = () => {
  isVisable.value = !isVisable.value;
}

const getApiKey = () => {
  return localStorage.getItem('apiKey')?? '';
}
const apiKey = ref(getApiKey());
const isVisable = ref(getApiKey() === '');

const saveApiKey = () => {
  isVisable.value = false;
  localStorage.setItem('apiKey', apiKey.value);
  window.location.reload();
}
</script>

<template>
  <div>
    <div class="container mx-auto">
      <input v-show="isVisable" v-model="apiKey" type="text" class="w-[480px] p-2 border-2 border-gray-300 rounded-lg"
        placeholder="API KEY" />
      <div class="flex space-x-2">
        <a class="text-blue-500 cursor-pointer underline" @click="showApiKey">{{ isVisable ? 'Hide' : 'Show API' }}</a>
        <a href="https://platform.openai.com/account/api-keys" target="_blank" class="text-blue-500 cursor-pointer underline">Manage Keys</a>
        <a v-if="isVisable" class="text-blue-500 cursor-pointer underline" @click="saveApiKey">Save</a>
      </div>
    </div>
    <div class="flex justify-center items-center min-h-screen">
      <HelloWorld v-if="getApiKey() !== ''" :api-key="getApiKey()" class="container mx-auto" />
    </div>
  </div>
</template>