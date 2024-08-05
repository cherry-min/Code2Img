<template>
  <main
    class="grid h-screen grid-rows-[1fr_auto] sm:grid-cols-[auto_1fr] sm:grid-rows-1"
  >
    <TheEditorFrame class="sm:col-start-2 sm:min-h-screen" />
    <TheSidebar class="sm:col-start-1" />
  </main>
</template>

<script setup lang="ts">
import TheEditorFrame from "~/components/TheEditorFrame.vue";
import TheSidebar from "~/components/TheSidebar.vue";
import { onMounted ,onUnmounted} from 'vue'
import { copyPngToClipboard, downloadPNG } from "~/lib/export";
const save =(event: KeyboardEvent) => {
    if (event.ctrlKey && event.key === 's') {
        event.preventDefault();
        downloadPNG(); 
      }
      if (event.ctrlKey && event.key === 'c') {
        event.preventDefault();
        copyPngToClipboard();
      }
  }
onMounted(()=>{
  console.log('mounted')
  window.addEventListener('keydown',save );

})
onUnmounted(()=>{
  console.log('unmounted')
  window.removeEventListener('keydown', save);
})

</script>
