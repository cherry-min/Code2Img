<script setup lang="ts">
import { twMerge } from "tailwind-merge";
import { computed } from "vue";
import { exportState } from "~/composables/export-state";
import { ExportState } from "~/enums";
import { copyPngToClipboard } from "~/lib/export";

const isFirefox = computed(() => {
  return navigator.userAgent.toLowerCase().includes("firefox");
});
</script>

<template>
  <BaseButton
    v-if="!isFirefox"
    :class="
      twMerge(
        'group w-full shrink-0 bg-emerald-600/30 px-4 text-emerald-500 hover:bg-emerald-600/40 sm:flex lg:w-[204px] ',
        $attrs.class as string,
      )
    "
    @click="copyPngToClipboard"
  >
    <IconClipboard
      width="16"
      class="transition-transform group-hover:rotate-6 group-hover:scale-110"
    />
    <span class="truncate whitespace-nowrap lg:overflow-visible">
      {{
        exportState === ExportState.PreparingToCopy
          ? "..."
          : exportState === ExportState.JustCopied
            ? "复制成功!"
            : exportState === ExportState.CopyFailure
              ? "Error! 尝试下载"
              : "复制图片到剪切板"
      }}
    </span>
  </BaseButton>
</template>
