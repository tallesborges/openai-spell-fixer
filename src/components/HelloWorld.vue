<script setup lang="ts">
import { Configuration, OpenAIApi } from "openai";
import { toast, type ToastOptions } from 'vue3-toastify';

import { Ref, ref } from 'vue'

const props = defineProps<{
  apiKey: string
}>()

const configuration = new Configuration({
  apiKey: props.apiKey,
});

const openai = new OpenAIApi(configuration);

const text: Ref<string> = ref('');
const correctedText: Ref<string> = ref('');
const responseJson: Ref<any> = ref({});
const history: Ref<any> = ref([]);

const send = async () => {
  const response = await openai.createCompletion({
    model: "text-davinci-003",
    prompt: `Correct the spelling and grammar of the following text:
    '''${text.value}'''
return a valid json with the keys:
"corrected": The corrected text
"explanations": Array of explanations for each correction
`,
    temperature: 0,
    max_tokens: 1000,
    top_p: 1,
    frequency_penalty: 0,
    presence_penalty: 0,
  });

  responseJson.value = JSON.parse(response.data.choices[0].text ?? '');
  history.value.unshift(responseJson.value);
  savetoStorage();
  navigator.clipboard.writeText(responseJson.value['corrected']);
}

const loadfromStorage = () => {
  const historyFromStorage = localStorage.getItem('history');
  if (historyFromStorage) {
    history.value = JSON.parse(historyFromStorage);
  }
}

const copy = (message: string) => {
  navigator.clipboard.writeText(message);
  toast("Copied", {
    autoClose: 1000,
    delay: 1,
    position: toast.POSITION.BOTTOM_CENTER,
  } as ToastOptions);
}

const deleteItem = (index: number) => {
  history.value.splice(index, 1);
  savetoStorage();
}

const editItem = (index: number) => {
  responseJson.value = history.value[index];
  text.value = history.value[index]['corrected'];
}

const reset = () => {
  text.value = '';
  responseJson.value = {};
}

const savetoStorage = () => {
  localStorage.setItem('history', JSON.stringify(history.value));
}

loadfromStorage();

</script>

<template>
  <div>
    <div class="flex flex-col max-w-4xl mx-auto">
      <a class="text-blue-500 cursor-pointer" @click="reset">Clear</a>
      <div class="flex flex-row space-x-2">
        <textarea class="border rounded w-full min-h-[160px] p-2" v-model="text" />
        <div class="border rounded w-full bg-gray-100 p-2 overflow-y-scroll min-h-full">
          <span v-text="responseJson['corrected']" />
          <ul class="list-disc list-inside">
            <li v-for="explanation in responseJson['explanations']" v-text="explanation" />
          </ul>
        </div>
      </div>
      <br>
      <button class="btn btn-blue" type="button" @click="send">Process</button>
      <br>
      <br>
      <span class="text-2xl font-extrabold">History</span>
      <div class="flex flex-col space-y-2">
        <div v-for="item in history" class="flex items-center space-x-2">
          <div class="bg-gray-100 p-2 rounded cursor-pointer w-full" @click="copy(item['corrected'])">
            <span v-text="item['corrected']" />
            <ul class="list-disc list-inside p-2">
              <li v-for="explanation in item['explanations']" v-text="explanation" />
            </ul>
          </div>
          <a class="text-blue-500 cursor-pointer" @click="editItem(history.indexOf(item))">Edit</a>
          <a class="text-red-500 cursor-pointer" @click="deleteItem(history.indexOf(item))">Delete</a>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.btn {
  @apply font-bold py-2 px-4 rounded;
}

.btn-blue {
  @apply bg-blue-500 text-white;
}

.btn-blue:hover {
  @apply bg-blue-700;
}
</style>