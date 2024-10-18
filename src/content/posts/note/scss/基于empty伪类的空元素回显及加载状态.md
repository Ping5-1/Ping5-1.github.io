---
title: 基于empty伪类的空元素回显及加载状态
published: 2024-04-05
description: ':empty伪类、::before伪元素、svg插图处理空内容样式，属性选择器处理加载状态'
image: ''
tags: ['css','scss']
category: '前端常见业务'
draft: false 
---

**mixin.scss**
```css
/** 空内容
  height:占位高度
**/
@mixin empty-block($height:220px) {
  display: block;
  position: relative;
  width: 100%;
  min-height: $height;//图片高度
  background-image: url('~@/assets/images/default-img/no-data.svg');
  background-repeat: no-repeat;
  background-position: center center;
  background-size: auto $height;
}

/** 空内容
  grid:是否是grid布局，如果在grid布局下，占满
  height:占位高度
**/
@mixin empty($grid: false, $height: 100%) {
  &:empty::before {
    content: '';
    @if $grid {
      grid-column: 1 / -1;
      grid-row: 1/-1;
    }
    @include empty-block($height);
  }
}

/** loading **/
@keyframes common-loading{
  0% {
    filter:opacity(0.25);
  }
  100%{
    filter:opacity(1);
  }
}

@mixin loading(){
  animation: common-loading 1.4s ease infinite
}
```
**应用一：表格**
common.scss
```css
/* 表格-空 */
.empty-table {
  .el-table[loading="true"] .el-table__empty-text{
    @include loading;
  }
  .el-table__empty-text {
    @include empty-block;
    color: transparent;
  }
}
/* 树形-空 */
.empty-tree {
  &[loading="true"] .el-tree__empty-block {
    @include loading;
  }
  .el-tree__empty-block {
    @include empty-block;
    .el-tree__empty-text {
      color: transparent;
    }
  }
}
```
demo.vue
```html
<el-table
  :loading="loading"
  :data="dataList"
  class="empty-table"
  height="100%"
>
  ...
</el-table>
```

**应用二：常规列表样式**
demo.scss
```css
/** grid布局 **/
    .data-list {
      padding: var(--space-middle);
      height: calc(100vh - 150px);
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      grid-template-rows: repeat(auto-fill, 126px);
      justify-content: space-around;
      grid-gap: var(--space-normal);
      overflow: auto;
      @include empty(true);
      /* 加载状态 */
      &[loading="true"]{
        @include loading;
      }
    }
/** 常规列表 **/
    .data-list2{
      &[loading="true"]{
        @include loading;
      }
      @include empty;
    }
```

**注：mixin样式继承**
`@extend .empty-table;`
