<template>
  <n-grid x-gap="22" :cols="2">
    <n-gi>
      <n-card title="LaTeX词典生成器">
        <h4 class="tag">单词：</h4>
        <n-input v-model:value="word" type="text" placeholder="" clearable />

        <h4 class="tag">中文：</h4>
        <n-input v-model:value="chinese" type="text" placeholder="" clearable />

        <h4 class="tag">词性：</h4>
        <n-select
          v-model:value="partOfSpeech"
          :options="partOfSpeechOptions"
          multiple
          ref="selectInstRef"
          @update:value="autoBlurSelect"
          placeholder="选择一个或多个词性"
        />

        <h4 class="tag">英文例句：</h4>
        <n-input
          v-model:value="enSen"
          type="textarea"
          placeholder=""
          clearable
        />

        <h4 class="tag">中文例句：</h4>
        <n-input
          v-model:value="chSen"
          type="textarea"
          placeholder=""
          clearable
        />

        <div class="button-box">
          <n-button type="info" @click="handleResult"> 生成结果 </n-button>

          <n-button type="tertiary" @click="clearInput"> 清除 </n-button>
        </div>
      </n-card>
    </n-gi>
    <n-gi>
      <n-card title="结果：" id="result-card">
        <n-input
          v-model:value="res"
          type="textarea"
          placeholder="这里会出现结果"
          ref="resultDom"
          :autosize="{
            minRows: 3,
            maxRows: 22,
          }"
          clearable
        />
      </n-card>
    </n-gi>
  </n-grid>
</template>

<script setup lang="ts">
import { ref, nextTick } from "vue";
import type { InputInst, SelectInst } from "naive-ui";

const word = ref("");
const chinese = ref("");
const partOfSpeech = ref();
const partOfSpeechOptions = ref([
  {
    label: "名词 n.",
    value: "n.",
    disabled: false,
  },
  {
    label: "动词 v.",
    value: "v.",
  },
  {
    label: "形容词 adj.",
    value: "adj.",
  },
  {
    label: "副词 adv.",
    value: "adv.",
    disabled: false,
  },
  {
    label: "冠词 art.",
    value: "art.",
  },
  {
    label: "代词 pron.",
    value: "pron.",
  },
  {
    label: "介词 prep.",
    value: "prep.",
  },
  {
    label: "数词 num.",
    value: "num.",
  },
  {
    label: "连词 conj.",
    value: "conj.",
  },
  {
    label: "感叹词 int.",
    value: "int.",
  },
]);
const enSen = ref("");
const chSen = ref("");
const res = ref("");

const resultDom = ref<InputInst | null>(null);
const selectInstRef = ref<SelectInst | null>(null);

const replaceAll = (string: string, search: string, replace: string) => {
  return string.split(search).join(replace);
};

const handleResult = () => {
  res.value += `\\entry{${word.value}}{${
    chinese.value
  }}{${partOfSpeech.value?.join(" ")}}{${enSen.value?.replace("%", "\\%")} ${
    chSen.value ? replaceAll(chSen.value, "%", "\\%") : ""
  }}\n\n`;
  clearInput();

  nextTick(() => {
    resultDom.value?.scrollTo({
      behavior: "smooth",
      top: 10000,
    });
  });
};

const clearInput = () => {
  word.value = "";
  chinese.value = "";
  partOfSpeech.value = null;
  enSen.value = "";
  chSen.value = "";
};

const autoBlurSelect = () => {
  selectInstRef.value?.blur();
};
</script>

<style scoped lang="scss">
.n-card {
  min-width: 300px;

  .tag {
    font-weight: 700;
    margin-top: 10px;
    margin-bottom: 10px;
  }

  .n-button {
    margin-top: 10px;
  }

  .button-box {
    display: flex;
    justify-content: space-around;
  }
}

#result-card {
  height: 100%;
}
</style>
