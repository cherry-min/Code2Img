<script setup lang="ts">
import BaseButton from "./BaseButton.vue";
import { getCodeBlocks } from "~/composables/block";
import { preview, store } from "~/composables/store";
import { BlockType } from "~/enums";

defineProps<{
  frameWidth: number;
}>();
</script>

<template>
  <div
    v-if="!preview"
    class="flex items-center justify-center overflow-hidden px-2 pt-2 pwa:sm:border-t pwa:sm:border-t-slate-900 pwa:sm:shadow-[inset_0_1px_0_rgb(30_30_37)]"
  >
    <div
      class="overflow-auto rounded-full border border-slate-700 bg-slate-800 px-3 py-2"
    >
      <div class="flex max-w-[100vw] items-center space-x-1">
        <BaseButton
          @click="store.editMode = 'code'"
          class="h-6 rounded-full px-2 hover:text-slate-100"
          :class="{
            'bg-slate-50 text-slate-900 hover:text-slate-900':
              store.editMode === 'code',
          }"
        >
          编辑
        </BaseButton>


        <BaseButton
          @click="store.editMode = 'highlight'"
          class="h-6 rounded-full px-2 hover:text-slate-100"
          :class="{
            'bg-slate-50 text-slate-900 hover:text-slate-900':
              store.editMode === 'highlight',
          }"
        >
          高亮
        </BaseButton>

        <BaseButton
          @click="store.editMode = 'focus'"
          class="h-6 rounded-full px-2 hover:text-slate-100"
          :class="{
            'bg-slate-50 text-slate-900 hover:text-slate-900':
              store.editMode === 'focus',
          }"
        >
          关注
        </BaseButton>

        <BaseButton
          @click="store.editMode = 'add'"
          class="h-6 whitespace-nowrap rounded-full px-2 hover:text-slate-100"
          :class="{
            'bg-slate-50 text-slate-900 hover:text-slate-900':
              store.editMode === 'add',
          }"
        >
          差异: 添加
        </BaseButton>

        <BaseButton
          @click="store.editMode = 'remove'"
          class="h-6 whitespace-nowrap rounded-full px-2 hover:text-slate-100"
          :class="{
            'bg-slate-50 text-slate-900 hover:text-slate-900':
              store.editMode === 'remove',
          }"
        >
          差异: 移除
        </BaseButton>

        <BaseButton
          @click="
            store.blocks.forEach((item) => {
              if (item.type !== BlockType.Code) {
                return;
              }

              item.transformations = [];
            })
          "
          class="h-6 rounded-full px-2 enabled:hover:text-slate-100 disabled:opacity-50"
          :disabled="
            getCodeBlocks().every((item) => item.transformations.length === 0)
          "
        >
          清理
        </BaseButton>
      </div>
    </div>
  </div>
</template>
