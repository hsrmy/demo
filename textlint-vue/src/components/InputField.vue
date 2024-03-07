<script setup lang="ts">
import { onBeforeUnmount, ref, watch } from "vue";
import type { TextlintMessage } from "@textlint/types";

const text = ref<string>("");
const lintResults = ref<string[]>([]);

const worker = new Worker("src/assets/textlint-worker.js");
worker.addEventListener("message", (event) => {
  const { data } = event;
  if (data.command === "lint:result") {
    if (data.result.messages.length > 0) {
      lintResults.value = data.result.messages.map(
        (message: TextlintMessage) =>
          `${message.loc.start.line}行目 ${message.loc.start.column}〜${message.loc.end.column}文字目: ${message.message}`
      );
    } else {
      lintResults.value = [];
    }
  }
});
const runTextlint = (text: string) => {
  worker.postMessage({
    command: "lint",
    text: text,
    ext: ".txt"
  });
};

onBeforeUnmount(() => worker.terminate());
watch(text, () => runTextlint(text.value));
</script>

<template>
  <div class="mx-auto flex max-w-7xl items-center justify-between py-6">
    <textarea
      v-model="text"
      name="text"
      id="editor"
      cols="30"
      rows="10"
      class="block p-3.5 w-full rounded-lg border border-gray-400"
      placeholder="何か入力してね！"
    ></textarea>
  </div>
  <div
    v-if="lintResults.length > 0"
    class="bg-yellow-400 mx-auto flex max-w-7xl items-center justify-between p-6"
  >
    <ul>
      <li v-for="(result, index) in lintResults" :key="index" class="mx-6">
        {{ result }}
      </li>
    </ul>
  </div>
</template>

<style scoped>
ul {
  list-style-type: initial;
}
</style>
