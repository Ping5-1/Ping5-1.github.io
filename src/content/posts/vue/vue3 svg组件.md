---
title: vue3 svg组件
published: 2024-09-29
description: 基于element+的svg组件
image: ''
tags: [vue,vue3,element+,组件]
category: '前端常见业务'
draft: false 
---

## 一、Svg组件化支持插件
vite.config.ts
```js
import { createSvgIconsPlugin } from "vite-plugin-svg-icons";//svgIcon
plugins:[
// svg组件化支持
    createSvgIconsPlugin({
      iconDirs: [pathResolve("../src/assets/svg")],
      // 指定symbolId格式
      symbolId: "icon-[name]",
      customDomId: "xxx-svgs", // 避免多项目互相影响
    }),
],
```
注：svg文件目录：/src/assets/svg/**
## 二、Svg组件
SvgIcon.vue
```html
<template>
  <component
    :class="svgClass"
    :is="renderIcon(name)"
    :style="size ? { width: size, height: size } : ''"
  />
</template>

<script>
  import { defineComponent, computed } from 'vue';
  import { renderIcon } from './index';

  export default defineComponent({
    name: 'SvgIcon',
    props: {
      name: {
        type: String
      },
      className: {
        type: String,
        default: ''
      },
      size: {
        type: [Number, String],
        default: ''
      }
    },
    setup(props) {
      const svgClass = computed(() => {
        if (props.className) {
          return 'svg-icon ' + props.className;
        } else {
          return 'svg-icon';
        }
      });
      return { svgClass, renderIcon };
    }
  });
</script>

<style scoped>
  .svg-icon {
    display: inline-block;
    width: 1em;
    height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
  }

  .svg-external-icon {
    background-color: currentColor;
    mask-size: cover !important;
    display: inline-block;
  }
</style>

```
index.ts
```js
import { App } from 'vue';
import { h } from 'vue';
import SvgIcon from './SvgIcon.vue';
import 'virtual:svg-icons-register';
import * as ElementPlusIconsVue from '@element-plus/icons-vue';
const svgModules = import.meta.glob('/src/assets/svg/*.svg');

// 图标名集合-element
const elementIconNames = Object.keys(ElementPlusIconsVue);
// 图标名集合-svg文件
const svgIconNames = Object.keys(svgModules).map((item) =>
  item.replace(/^\/src\/assets\/svg\/(.*).svg$/, '$1')
);

const svgIconPlugin = {
  install(app: App): void {
    // 全局挂载
    app.component('SvgIcon', SvgIcon);
  }
};

// 是ele+图标
export const isElementPlusIcon = (name) => {
  return name?.startsWith('el-') || elementIconNames.includes(name);
};

// 是本地svg图标
export const isSvgIcon = (name) => {
  return svgIconNames.includes(name);
};

function toPascalCase(name: string): string {
  // 分割字符串并处理每个部分
  return name
    .split('-')
    .map((part) => part.charAt(0).toUpperCase() + part.slice(1))
    .join('');
}

/* 根据图标名获取图标 */
export const renderIcon = (name) => {
  if (!name) return;

  // element+
  if (isElementPlusIcon(name)) {
    const componentName = name.replace('el-', '');
    return ElementPlusIconsVue[toPascalCase(componentName)];
  }

  // svg-icon
  if (isSvgIcon(name)) {
    return h(
      'svg',
      {
        class: 'svg-icon'
      },
      {
        default: () => [
          h('use', {
            'xlink:href': '#icon-' + name
          })
        ]
      }
    );
  }

  return;
};

export default svgIconPlugin;
```

## 三、引入及应用
### main.ts引入
```js
import SvgIcon from '@/components/svgIcon'; // svg封装插件
// 全局注册矢量图标库
app.use(SvgIcon);
```
### 应用一、Element+ icon：本地svg图标
```html
<el-input
  v-model="keyword"
  :prefix-icon="renderIcon('login-key')"
 />
<script setup lang="ts">
  import { renderIcon } from '@/components/SvgIcon';
</script>
```
### 应用二、Element+ icon：Element自带图标
```html
<el-button :icon="renderIcon('el-search')">
  搜索
</el-button>
<script setup lang="ts">
  import { renderIcon } from '@/components/SvgIcon';
</script>
```
### 应用三、常规组件使用
```html
<SvgIcon name="back_top" />
```
