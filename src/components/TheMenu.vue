<script setup lang="ts">
import { useMagicKeys, whenever } from "@vueuse/core";
import {
  MenubarContent,
  MenubarItem,
  MenubarMenu,
  MenubarPortal,
  RadioGroupRoot,
  RadioGroupItem,
  MenubarRoot,
  MenubarTrigger,
} from "radix-vue";
import { ref } from "vue";
import {
  addEditorBlock,
  addMarkdownBlock,
  getCodeBlocks,
} from "~/composables/block";
import { store } from "~/composables/store";
import { BlockType } from "~/enums";
import { copyPngToClipboard, downloadPNG } from "~/lib/export";

const currentMenu = ref("");
const keys = useMagicKeys();
// const isFirefox = computed(() => {
//   return navigator.userAgent.toLowerCase().includes("firefox");
// });

whenever(() => keys.ctrl_e.value && !keys.current.has("shift"), addEditorBlock);
whenever(keys.ctrl_shift_e, addMarkdownBlock);

function clearLineDecorations() {
  store.value.blocks.forEach((item) => {
    if (item.type !== BlockType.Code) {
      return;
    }

    item.transformations = [];
  });
}
</script>
©
<template>
  <div>
    <MenubarRoot
      v-model="currentMenu"
      class="flex items-center overflow-auto border-b border-b-zinc-800 bg-zinc-900 pwa:sm:border-t pwa:sm:border-t-black pwa:sm:shadow-[inset_0_1px_0_rgb(39_39_42)]"
    >
      <!-- <MenubarMenu value="help" v-if="store.editMode === 'code'">
        <MenubarTrigger class="menubar-trigger">
          关于
          <i-radix-icons:chevron-down class="ml-1" />
        </MenubarTrigger>

        <MenubarPortal>
          <MenubarContent
            class="menubar-content"
            :side-offset="8"
            :align-offset="8"
          >
            <MenubarItem
              as="a"
              class="menubar-item group"
              href="https://twitter.com/Idered"
            >
              <i-fa6-brands:x-twitter class="mr-2 size-4" />
              Follow on X
            </MenubarItem>
            <MenubarItem
              as="a"
              class="menubar-item group"
              href="https://github.com/Idered/chalk.ist"
            >
              <i-fa6-brands:github class="mr-2 size-4" />
              View source on GitHub
            </MenubarItem>
            <MenubarItem
              as="a"
              class="menubar-item group"
              href="https://www.buymeacoffee.com/idered"
            >
              <i-ph:coffee class="mr-2 size-4" />
              Buy me a coffee
            </MenubarItem>
          </MenubarContent>
        </MenubarPortal>
      </MenubarMenu> -->

      <MenubarMenu value="blocks" v-if="store.editMode === 'code'">
        <MenubarTrigger class="menubar-trigger">
          <span>代码块</span>
          <i-radix-icons:chevron-down class="ml-1" />
        </MenubarTrigger>
        <MenubarPortal>
          <MenubarContent class="menubar-content" :side-offset="8">
            <MenubarItem class="menubar-item group" @click="addEditorBlock">
              添加代码块
              <div class="menubar-item-shortcut">⌃ E</div>
            </MenubarItem>
            <MenubarItem class="menubar-item group" @click="addMarkdownBlock">
              添加markdown代码块
              <div class="menubar-item-shortcut">⇧ ⌃ E</div>
            </MenubarItem>
          </MenubarContent>
        </MenubarPortal>
      </MenubarMenu>

      <MenubarMenu value="line decorations" v-if="store.editMode === 'code'">
        <MenubarTrigger class="menubar-trigger">
          <span>高级</span>
          <i-radix-icons:chevron-down class="ml-1" />
        </MenubarTrigger>

        <MenubarPortal>
          <MenubarContent class="menubar-content" :side-offset="8">
            <MenubarItem
              class="menubar-item group"
              @click="store.editMode = 'highlight'"
            >
              高亮
            </MenubarItem>
            <MenubarItem
              class="menubar-item group"
              @click="store.editMode = 'focus'"
            >
              聚焦
            </MenubarItem>
            <MenubarItem
              class="menubar-item group"
              @click="store.editMode = 'add'"
            >
              差异: 代码添加
            </MenubarItem>
            <MenubarItem
              class="menubar-item group"
              @click="store.editMode = 'remove'"
            >
              差异: 代码移除
            </MenubarItem>
            <MenubarItem
              class="menubar-item group"
              @click="clearLineDecorations"
              :disabled="
                getCodeBlocks().every(
                  (item) => item.transformations.length === 0,
                )
              "
            >
              清理样式
            </MenubarItem>
          </MenubarContent>
        </MenubarPortal>
      </MenubarMenu>

      <MenubarMenu value="export" v-if="store.editMode === 'code'">
        <MenubarTrigger class="menubar-trigger text-white">
          <div
            v-if="store.lastCopyMethod === 'copy_png'"
            :tabindex="0"
            @pointerdown.stop.prevent="copyPngToClipboard"
            class="-ml-1 mr-2 flex size-8 items-center justify-center rounded hover:bg-slate-700"
          >
            <i-radix-icons:clipboard class="size-4" />
          </div>
          <div
            v-if="store.lastCopyMethod === 'download_png'"
            :tabindex="0"
            @pointerdown.stop.prevent="downloadPNG"
            class="-ml-1 mr-2 flex size-8 items-center justify-center rounded hover:bg-slate-700"
          >
            <i-radix-icons:download class="size-4" />
          </div>
          <span>导出</span>
          <i-radix-icons:chevron-down class="ml-1" />
        </MenubarTrigger>

        <MenubarPortal>
          <MenubarContent class="menubar-content" :side-offset="8">
            <MenubarItem class="menubar-item" @click="copyPngToClipboard">
              <i-radix-icons:clipboard class="mr-2 size-4" />
              <span>复制图片</span>
            </MenubarItem>
            <MenubarItem class="menubar-item" @click="downloadPNG">
              <i-radix-icons:download class="mr-2 size-4" />
              <span>下载PNG</span>
            </MenubarItem>
          </MenubarContent>
        </MenubarPortal>
      </MenubarMenu>

      <RadioGroupRoot
        class="flex"
        v-model="store.editMode"
        v-if="store.editMode !== 'code'"
      >
        <RadioGroupItem value="highlight" class="menubar-item-radio">
          高亮
        </RadioGroupItem>
        <RadioGroupItem value="focus" class="menubar-item-radio">
          聚焦
        </RadioGroupItem>
        <RadioGroupItem value="add" class="menubar-item-radio">
          差异: 新增
        </RadioGroupItem>
        <RadioGroupItem value="remove" class="menubar-item-radio">
          差异: 移除
        </RadioGroupItem>
      </RadioGroupRoot>

      <button
        v-if="store.editMode !== 'code'"
        class="menubar-trigger group cursor-pointer"
        @click="clearLineDecorations"
        :disabled="
          getCodeBlocks().every((item) => item.transformations.length === 0)
        "
      >
        清理
      </button>

      <BaseButton
        v-if="store.editMode !== 'code'"
        @click="store.editMode = 'code'"
        class="ml-4 h-8 bg-blue-700 px-4 text-white"
      >
        完成
      </BaseButton>

      <!-- <div
        class="ml-auto hidden items-center space-x-2 pr-2 lg:flex"
        v-if="store.editMode === 'code'"
      >
        <ExportToClipboardButton class="h-8" />
        <ExportToPNGButton class="h-8" />
      </div> -->
    </MenubarRoot>
  </div>
</template>
