---
title: 指令：el-select加载更多
published: 2024-04-13
description: '在下拉选项特别多的情况下，需要按分页去加载更多'
image: ''
tags: [vue]
category: '前端常见业务'
draft: false 
---
```html
<el-select v-selectMore="loadMore">...</el-select>
```
```js
const selectMore = {
  bind(el, binding) {
    // 获取element-ui定义好的scroll盒子
    let select_dom = el.querySelector('.el-select-dropdown .el-select-dropdown__wrap');
    select_dom.addEventListener('scroll', function () {
      const CONDITION = this.scrollHeight - this.scrollTop <= this.clientHeight;
      if (CONDITION) {
        binding.value();
      }
    });
  }
};

export default selectMore;
```