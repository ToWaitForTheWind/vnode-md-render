<template>
    <div class="md-content">
      <component
        v-for="(node, index) in vNodes"
        :key="index"
        :is="node"
      />
    </div>
  </template>
  
  <script setup>
  import { computed, h } from 'vue'
  import { marked } from 'marked'
  
  const props = defineProps({
    content: {
      type: String,
      required: true
    }
  })
  
  // 将token转换为vnode的函数
  const tokenToVNode = (token) => {
    switch (token.type) {
      case 'heading':
        return h(`h${token.depth}`, {}, token.text)
      case 'paragraph':
        return h('p', {}, token.text)
      case 'list':
        return h(token.ordered ? 'ol' : 'ul', {},
          token.items.map(item => h('li', {}, item.text))
        )
      case 'code':
        return h('pre', {}, [h('code', {}, token.text)])
      default:
        return h('div', {}, token.raw)
    }
  }
  
  const vNodes = computed(() => {
    // 使用marked的词法分析器获取tokens
    const tokens = marked.lexer(props.content)
    // 将tokens转换为vnodes
    return tokens.map(token => tokenToVNode(token))
  })
  </script>
  
  <style scoped>
  .md-content {
    padding: 10px;
  }
  </style>
  