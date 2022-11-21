<template>
  <div v-for="(item, index) in data" :key="index">
    <a-sub-menu
      v-if="item?.children?.length > 0"
      :title="item.title"
      :key="item.key"
    >
      <menu-item :data="item.children" />
    </a-sub-menu>
    <a-menu-item v-else :key="item.key" @click="clickMenu(item)">{{
      item.title
    }}</a-menu-item>
  </div>
</template>
<script lang="ts">
export default {
  name: 'menu-item',
};
</script>

<script setup lang="ts">
import { marked } from 'marked';

const props = defineProps({
  data: {
    type: Array,
    default: () => [],
  },
});
const clickMenu = (item) => {
  item.module().then((m: any) => {
    let html = marked(m.default);
    // @ts-ignore
    const dom = document.getElementById('markdown');
    // @ts-ignore
    dom.innerHTML = html;
  });
};
</script>

<style scoped></style>
