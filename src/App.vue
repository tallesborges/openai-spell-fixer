<script setup lang="ts">
import { ref } from 'vue';
import HelloWorld from './components/HelloWorld.vue'

const showApiKey = () => {
  isApiVisible.value = !isApiVisible.value;
}
const showPrompot = () => {
  isPrompotVisible.value = !isPrompotVisible.value;
}

const getApiKey = () => {
  return localStorage.getItem('apiKey')?? '';
}

const defaultPrompot = `Correct the spelling, grammar, and clarity issues in the given text and output the corrected text in JSON format with only the key 'corrected' and an array of explanations for the corrections made. Ensure that the JSON returned is valid and can be parsed by a JSON parser.
Example output: {"corrected": "The quick brown fox jumps over the lazy dog.", "explanations": ["The word 'jumps' was corrected to 'jump' because it is the correct conjugation of the verb 'jump'"]}
Text: "###text###"
Output:`;

const getPrompt = () => {
  return localStorage.getItem('prompt') || defaultPrompot;
}

const apiKey = ref(getApiKey());
const prompt = ref(getPrompt());
const isApiVisible = ref(getApiKey() === '');
const isPrompotVisible = ref(false);

const saveApiKey = () => {
  isApiVisible.value = false;
  localStorage.setItem('apiKey', apiKey.value);
  window.location.reload();
}

const savePrompt = () => {
  isPrompotVisible.value = false;
  localStorage.setItem('prompt', prompt.value);
  window.location.reload();
}
</script>

<template>
  <div>
    <div class="container mx-auto">
      <input v-show="isApiVisible" v-model="apiKey" type="text" class="w-[480px] p-2 border-2 border-gray-300 rounded-lg"
        placeholder="API KEY" />
        <textarea v-show="isPrompotVisible" v-model="prompt" type="text" class="w-full h-36 p-2 border-2 border-gray-300 rounded-lg"/>        
      <div class="flex space-x-2">
        <a class="text-blue-500 cursor-pointer underline" @click="showApiKey">{{ isApiVisible ? 'Hide API' : 'Show API' }}</a>
        <a v-if="isApiVisible" class="text-blue-500 cursor-pointer underline" @click="saveApiKey">Save API</a>
        <a href="https://platform.openai.com/account/api-keys" target="_blank" class="text-blue-500 cursor-pointer underline">Manage Keys</a>
        <a class="text-blue-500 cursor-pointer underline" @click="showPrompot">{{ isPrompotVisible ? 'Hide Prompot' : 'Show Prompot' }}</a>
        <a v-if="isPrompotVisible" class="text-blue-500 cursor-pointer underline" @click="savePrompt">Save Prompt</a>
      </div>
    </div>
    <div class="flex justify-center items-center min-h-screen">
      <HelloWorld v-if="getApiKey() !== ''" :api-key="getApiKey()" :prompt="getPrompt()" class="container mx-auto" />
    </div>
  </div>
</template>