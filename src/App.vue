<template>
  <div class="container">
    <div class="editor">
      <textarea v-model="mdContent"></textarea>
    </div>
    <div class="preview">
      <MDContent v-model="mdContent" />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import MDContent from './components/MDContent.vue'

const mdContent = ref('')
</script>

<style lang="scss" scoped>
.container {
  display: flex;
  height: 100vh;
  padding: 20px;
  gap: 20px;
}

.editor, .preview {
  flex: 1;
  border: 1px solid #ccc;
  padding: 10px;
  border-radius: 4px;
}

.editor textarea {
  width: 100%;
  height: 100%;
  border: none;
  outline: none;
  resize: none;
}

.preview {
  overflow-y: auto;
  
  :deep(h1) {
    color: #2c3e50;
  }
  
  :deep(p) {
    line-height: 1.6;
  }
}
</style>
