<template>
  <div class="catelog">
    <a-menu class="cate" mode="inline" v-model:openKeys="openKeys">
      <div class="web-info">
        <img
          class="logo"
          src="http://img.wawow.xyz/imgs/logo.png@tcys"
          alt=""
        />
      </div>
      <MenuItem :data="state.catelog" />
    </a-menu>
  </div>
  <div id="markdown" class="markdown-body md-area"></div>
</template>

<script setup lang="ts">
import { onMounted, reactive } from 'vue';
import './assets/theme/channing-cyan.css';
import MenuItem from './components/MenuItem.vue';

let state = reactive<any>({
  catelog: [],
  modules: {},
});

let openKeys = reactive<string[]>(['./docs/']);
const setCatelog = (input: string[]) => {
  let root: { key: string; title: string; children: never[] }[] = [];
  for (let i = 0; i < input.length; i++) {
    let chain = input[i].split('/');
    let currentHierarchy = root;
    for (let j = 0; j < chain.length; j++) {
      let wantedNode = chain[j];
      if (wantedNode === '') {
        continue;
      }
      let lastHierarchy = currentHierarchy;

      // 遍历root是否已有该层级
      for (let k = 0; k < currentHierarchy.length; k++) {
        if (currentHierarchy[k].title === wantedNode) {
          currentHierarchy = currentHierarchy[k].children;
          break;
        }
      }

      if (lastHierarchy === currentHierarchy) {
        let key;
        if (j === chain.length - 1) {
          key = input[i];
        } else {
          key = chain.slice(0, j + 1).join('/') + '/';
        }
        let newNode = {
          key: key,
          title: wantedNode,
          module: state.modules[key],
          children: [],
        };
        // 文件，最后一个字符不是"/“符号
        if (j === chain.length - 1) {
          delete newNode.children;
        }
        currentHierarchy.push(newNode);
        currentHierarchy = newNode.children;
      }
    }
  }
  if (root.length === 1) {
    root = root[0].children;
    if (root[0].title === 'docs') {
      root[0].title = '源码阅读';
    }
  }
  return root;
};
onMounted(async () => {
  const modules = import.meta.glob('./docs/**');
  for (const item in modules) {
    // 修改md引入格式
    modules[item] = () => import(item + '?raw');
  }
  state.modules = modules;
  state.catelog = setCatelog(Object.keys(modules));
});
</script>

<style scoped>
.catelog {
  display: flex;
  left: 0;
  top: 0;
  width: 20vw;
  min-height: 100vh;
}
.cate {
  width: 100%;
}
.md-area {
  width: 80vw;
  min-height: 100vh;
  padding: 30px 80px;
}
.web-info {
  height: 5vw;
  width: 100%;
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
  border-bottom: 1px solid #4dd0e1;
}
.logo {
  width: 13.5vw;
  height: 2.5vw;
}
</style>
