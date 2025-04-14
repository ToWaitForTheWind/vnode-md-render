<template>
  <div class="md-content">
    <template v-for="item in vdom">
      <component v-if="item.type" :is="item"></component>
      <template v-else>{{ item }}</template>
    </template>
  </div>
  <div class="html-content" v-html="htmlStr"></div>
</template>

<script setup>
import { ref, watchEffect, h } from 'vue';
import MarkdownIt from 'markdown-it';
import hljs from 'highlight.js';
import 'highlight.js/styles/atom-one-dark.css';

const modelValue = defineModel();
const md = MarkdownIt({
  highlight: function (str, lang) {
    if (lang && hljs.getLanguage(lang)) {
      try {
        return hljs.highlight(str, { language: lang }).value;
      } catch (__) {}
    }
    return '';
  }
});
const htmlStr = ref('');
const vdom = ref([]);

const htmlToVNodes = (html) => {
  const parser = new DOMParser();
  const doc = parser.parseFromString(html, 'text/html');

  function convertElement(element) {
    const children = Array.from(element.childNodes).map(node => {
      if (node.nodeType === Node.ELEMENT_NODE) {
        return convertElement(node);
      }
      return node.textContent; // 文本节点直接返回文本内容
    });

    const props = {};
    for (let i = 0; i < element.attributes.length; i++) {
      const attr = element.attributes[i];
      props[attr.name] = attr.value;
    }

    return h(element.tagName, props, children);
  }

  return convertElement(doc.body);
}
watchEffect(() => {
  htmlStr.value = md.render(modelValue.value);
  vdom.value = htmlToVNodes(htmlStr.value)?.children || [];
});

</script>

<style scoped>
.md-content {
  padding: 10px;
}
</style>
