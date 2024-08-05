---
title: 指令：DOM拖拽
published: 2024-01-27
description: dom拖拽指令，拖拽操作区及拖拽状态可配置
image: ''
tags: [vue]
category: '前端常见业务'
draft: false 
---
directives.js:
```js
/**
 * v-dialogDrag 弹窗拖拽
 * @params operate {String} 拖拽项的data-drag-name   
 * @params move 
 */
const dialogDrag = {
  // el, binding, vnode, oldVnode
  inserted(el, binding,vnode) {
    const { operate, move } = binding.value;
    if(typeof binding.value !== 'object') {
      throw new Error('参数错误！');
    }
    if (!move || operate === false || operate === '') return;
    // 获取拖拽内容头部
    const selector = `[data-drag-name="${operate}"]`;
    const operateElList = el.querySelectorAll(selector);
    let dragDom;
    if(move === true) {
      dragDom = el;
    }else{
      dragDom = document.querySelector(`[class*="${move}"]`);
    }
    if (!dragDom || !operateElList?.length) return;

    // 获取原有属性 ie dom元素.currentStyle 火狐谷歌 window.getComputedStyle(dom元素, null);
    const currentStyle = dragDom.currentStyle || window.getComputedStyle(dragDom, null);
    dragDom.style.position = 'absolute';
    // 鼠标按下事件
    operateElList.forEach((operateEl) => {
      operateEl.style.cursor = 'move';
      const {draggable} = vnode.context;
      operateEl.onmousedown = ($event) => moveFn($event,draggable, operateEl, currentStyle, dragDom);
    });
  },
};

// 移动
function moveFn(e, draggable,operateEl, sty, dragDom) {
  if(draggable === false) return;
  
  e.stopPropagation();
  // 鼠标按下，计算当前元素距离可视区的距离 (鼠标点击位置距离可视窗口的距离)
  const disX = e.clientX - operateEl.offsetLeft;
  const disY = e.clientY - operateEl.offsetTop;

  // 获取到的值带px 正则匹配替换
  let styL, styT;

  // 注意在ie中 第一次获取到的值为组件自带50% 移动之后赋值为px
  if (sty.left.includes('%')) {
    styL = +document.body.clientWidth * (+sty.left.replace(/\%/g, '') / 100);
    styT = +document.body.clientHeight * (+sty.top.replace(/\%/g, '') / 100);
  } else {
    styL = +sty.left.replace(/\px/g, '');
    styT = +sty.top.replace(/\px/g, '');
  }

  // 鼠标拖拽事件
  document.onmousemove = function (e) {
    // 通过事件委托，计算移动的距离 （开始拖拽至结束拖拽的距离）
    const l = e.clientX - disX;
    const t = e.clientY - disY;

    let finallyL = l + styL;
    let finallyT = t + styT;

    // 移动当前元素
    dragDom.style.left = `${finallyL}px`;
    dragDom.style.top = `${finallyT}px`;
  };

  document.onmouseup = function () {
    document.onmousemove = null;
    document.onmouseup = null;
  };
}

export default {
  dialogDrag
};
```

使用：

```html
 <div  v-dialogDrag="{ operate: operateUUid, move: true,draggable:draggable}">
    <div :data-drag-name="operateUUid" />
 </div>
 import {dialogDrag} from '@/utils/directives.js'; // 指令插件
 export default {
  directives: {
    dialogDrag: dialogDrag
  },
  data(){
    operateUUid:1234,// 操作区id
    draggable:true// 是否可拖拽
  },
}
```