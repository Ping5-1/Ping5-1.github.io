---
title: 指令：移除输入框中的空格
published: 2024-04-13
description: '包括首尾空格和内部空格禁用'
image: ''
tags: [vue]
category: '前端常见业务'
draft: false 
---
```js
/**
 * 去除两边空格
 * 使用 <el-input v-model="xxx" v-trim></el-input>
 */
function getInput(el) {
  let inputEle;
  if (el.tagName !== 'INPUT') {
    inputEle = el.querySelector('input');
  } else {
    inputEle = el;
  }
  return inputEle;
}
function dispatchEvent(el, type) {
  let evt = document.createEvent('HTMLEvents');
  evt.initEvent(type, true, true);
  el.dispatchEvent(evt);
}
const Trim = {
  inserted: el => {
    let inputEle = getInput(el);
    const handler = function (event) {
      const newVal = event.target.value.trim();
      if (event.target.value !== newVal) {
        event.target.value = newVal;
        dispatchEvent(inputEle, 'input');
      }
    };
    el.inputEle = inputEle;
    el._blurHandler = handler;
    inputEle.addEventListener('blur', handler);
  },
  unbind(el) {
    const { inputEle } = el;
    inputEle.removeEventListener('blur', el._blurHandler);
  }
};
export default Trim;
```
升级版：区分禁用空格与trim

```js
const trimSpacing = {
  inserted(el, binding, vnode) {
    const { spaceDisabled = false } = binding.value || {};// 禁止空格：默认false
    if(binding.value && typeof binding.value !== 'object') {
      throw new Error('参数错误！');
    }

    let inputEle;
    // 判断是否为 Element UI 表单组件
    if (vnode.componentInstance) {
      const component = vnode.componentInstance;
      // 获取元素及其子元素的input元素数组，一般数组长度为1
      const inputEles = getInputEle(component.$el);
      if(inputEles.length) {
        inputEle = inputEles[0];
      }
    } else {
      // 原生表单
      inputEle = el;
    }
    if(spaceDisabled) {
      inputEle.addEventListener('keydown', spaceDisableFn);
    }else{
      const handler = function (event) {
        const newVal = event.target.value.trim();
        if (event.target.value !== newVal) {
          event.target.value = newVal;
          dispatchEvent(inputEle, 'input');
        }
      };
      el._blurHandler = handler;
      inputEle.addEventListener('blur', handler);
    }
  },
  unbind(el, binding, vnode) {
    const { inputEle } = el;
    const { spaceDisabled = 'false' } = binding.value || {};// 禁止空格：默认false
    if(spaceDisabled) {
      inputEle.removeEventListener('keydown', spaceDisableFn);
    }else{
      inputEle.removeEventListener('blur', el._blurHandler);
    }
  }
};

// el事件
function dispatchEvent(el, type) {
  let evt = document.createEvent('HTMLEvents');
  evt.initEvent(type, true, true);
  el.dispatchEvent(evt);
}

// 输入结果trim
function trimFn(inputEle,event) {
  const newVal = event.target.value.trim();
  if (event.target.value !== newVal) {
    event.target.value = newVal;
    dispatchEvent(inputEle, 'input');
  }
}

// 禁止输入空格
function spaceDisableFn(e) {
  console.log('spaceDisableFn');
  if (e.key === ' ') {
    e.preventDefault();
  }
}

// 获取input元素
function getInputEle(ele) {
  let inputEles = [];
  if (ele.tagName === 'INPUT') {
    inputEles.push(ele);
  }
  if(ele.children) {
    for(const childEle of ele.children) {
      inputEles = inputEles.concat(getInputEle(childEle));
    }
  }

  return inputEles;
}

export default trimSpacing;
```
禁用空格
```html
<el-input 
   v-model="value"
   v-trim="{spaceDisabled:true}"
/>
```

去除首尾空格
```html
<el-input 
   v-model="value"
   v-trim="{spaceDisabled:true}"
/>
```