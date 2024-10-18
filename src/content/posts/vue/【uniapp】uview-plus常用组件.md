---
title: 【uniapp】uview-plus常用组件
published: 2024-09-29
description: 基于uview plus的常用组件组件
image: ''
tags: [vue,vue3,uview plus,组件]
category: '前端常见业务'
draft: false 
---

### 常用下拉单选
com-select.vue
```html
<template>
  <view>
    <up-icon
      v-if="mode === 'text'"
      name="arrow-down"
      :label="inputValue || placeholder"
      labelPos="left"
      :labelSize="fontSize"
      :size="14"
      @click="openActionSheet"
    ></up-icon>
    <up-input
      v-else
      v-model="inputValue"
      clearable
      :fontSize="fontSize"
      suffixIcon="arrow-down"
      :placeholder="placeholder"
      @click="openActionSheet"
    />
    <up-picker
      :show="visible"
      :columns="columns"
      :closeOnClickOverlay="true"
      cancelText="清空"
      :keyName="labelName"
      @confirm="onSelectAction"
      @close="visible = false"
      @cancel="handleClear()"
    />
  </view>
</template>

<script setup lang="ts">
import { ref } from 'vue';
let visible = ref(false);
let inputValue = ref('');

// 定义props
const props = defineProps({
  value: String,
  columns: Array,
  placeholder: String,
  labelName: String,
  valueName: String,
  mode: String,
  fontSize: [String , Number]
});
props.labelName ? (inputValue.value = '全部') : (inputValue.value = '');

// 点击文本框，显示底部选项面板
const openActionSheet = () => {
  visible.value = true;
};

// 选择某个选项，关闭选项面板
const emit = defineEmits(['input']);
const onSelectAction = (action: any) => {
  visible.value = false;
  props.labelName
    ? (inputValue.value = action.value[0]?.[props.labelName])
    : (inputValue.value = action.value[0]);
  if (props.labelName) {
    emit('input', action.value[0]?.[props.valueName || 'value']);
  } else {
    emit('input', inputValue.value);
  }
};

/** 清空 */
const handleClear = () => {
  visible.value = false;
  props.labelName ? (inputValue.value = '全部') : (inputValue.value = '');
  emit('input', '');
};
</script>

<style lang="scss"></style>
```

### 常用tag
common-tag.vue
```html
<template>
  <up-tag
    class="com-tag"
    :text="text"
    :type="currentItem.color"
    :plain="currentItem.plain"
  ></up-tag>
</template>

<script setup lang="ts">
import { computed } from 'vue';
interface ListItem {
  value?: string;
  label?: string;
  color?: string;
  plain?: boolean;
}
interface Props {
  value: string | number;
  list: ListItem[];
  labelName?: string;
  valueName?: String;
  shape?: String;
}

// 定义props
const props = withDefaults(defineProps<Props>(), {
  list: () => [],
  labelName: () => 'label',
  valueName: () => 'value'
});
const currentItem = computed(() => {
  return (
    (props.list || []).find(
      (item: ListItem) => String(item[props.valueName]) === String(props.value)
    ) || {}
  );
});
const text = computed(() => {
  return currentItem.value[props.labelName];
});
</script>

<style lang="scss"></style>
```

### 树形
common-tree.vue
```html
<template>
  <view class="tree-panel">
    <tree-node
      class="tree-box"
      :data="treeShowData"
      :labelKey="labelKey"
      :idKey="idKey"
      @click="checkNode"
    >
      <template #default="{ node }">
        <slot :node="node">{{ node[labelKey] }}</slot>
      </template>
    </tree-node>
    <view v-show="!treeShowData.length" class="empty">
      暂无数据
    </view >
  </view>
</template>
<script setup lang="ts">
  import TreeNode from './tree-node.vue';
  import { ref, watch, computed, defineExpose } from 'vue';

  export interface Props {
    data: any[];
    treeProp?: any;
    checkable?: boolean;
  }

  const props = withDefaults(defineProps<Props>(), {
    treeProp: {},
    checkable: false,
  });

  const emit = defineEmits<{
    (e: 'check', checkList: any[]): void;
    (e: 'click', current: object): void;
  }>();

  defineExpose({
    clearCheck,
    inputSearch,
    inputClear,
    checkNodeByKey,
  });

  const labelKey = computed(() => {
    return props?.treeProp?.labelKey || 'label';
  });
  const idKey = computed(() => {
    return props?.treeProp?.idKey || 'id';
  });

  const keyword = ref(''); //查询关键字
  const checkList = ref([] as any); //选中列表
  const treeShowData = ref(props.data as any); //树形数据-实际显示
  watch(
    () => props.data,
    (val) => {
      treeShowData.value = val || [];
    },
    { immediate: true },
  );

  // 搜索-确认
  const inputSearch = (e: any) => {
    keyword.value = e.value;
    if (!keyword.value) {
      treeShowData.value = props.data;
    } else {
      treeShowData.value = searchTree(props.data, keyword.value, true);
    }
  };
  // 搜索-清空
  const inputClear = () => {
    treeShowData.value = props.data;
  };
  // 搜索-树形数据处理
  const searchTree = (tree: any[], keyword: string, includeChildren: boolean = false) => {
    const newTree = [] as any;
    for (let i = 0; i < tree.length; i++) {
      const node = tree[i];
      if (node[labelKey.value]?.includes(keyword)) {
        // 如果当前节点符合条件，则将其复制到新的树形结构中，并根据 includeChildren 参数决定是否将其所有子节点也复制到新的树形结构中
        newTree.push({
          ...node,
          children: includeChildren ? searchTree(node.children || [], '', true) : [],
        });
      } else if (node.children) {
        // 如果当前节点不符合条件且存在子节点，则递归遍历子节点，以继续搜索
        const result = searchTree(node.children, keyword, true);
        if (result.length > 0) {
          // 如果子节点中存在符合条件的节点，则将其复制到新的树形结构中
          newTree.push({ ...node, children: result });
        }
      }
    }
    return newTree;
  };
  // 点击树节点
  const checkNode = (item: any, checked?: boolean) => {
    emit('click', item); // 点击事件
    if (!props.checkable) return;

    const isCheckable = !!props.treeProp?.checkFn?.(item);
    if (!isCheckable) return;

    if (typeof checked !== 'undefined') {
      item._checked = checked;
    } else {
      if (typeof item._checked === 'undefined') {
        item._checked = false;
      }
      item._checked = !item._checked;
    }
    const checkIndex = checkList.value.findIndex(
      (it: any) => it[idKey.value] === item[idKey.value],
    );
    if (checkIndex === -1 && item._checked) {
      checkList.value.push(item);
    }
    if (checkIndex !== -1 && !item._checked) {
      checkList.value.splice(checkIndex, 1);
    }
    emit('check', checkList.value);
  };
  // 清空选中
  const clearCheck = () => {
    checkList.value.forEach((item: any) => {
      item._checked = false;
    });
    checkList.value = [];
    emit('check', checkList.value, null);
  };
  // 根据id查找树结点
  const getNodeById = (treeNodes: any[], id: string): any => {
    for (let node of treeNodes) {
      if (node[idKey.value] === id) return node;
      else if (node.children?.length) {
        const result2 = getNodeById(node.children, id);
        if (result2) {
          return result2;
        }
      }
    }
    return null;
  };
  const checkNodeByKey = (id: string, checked: boolean) => {
    const item = getNodeById(props.data, id);
    if (!item) return;

    checkNode(item, checked);
  };
</script>

<style lang="scss" scoped>
  .tree-panel .tree-box {
    height: calc(100% - 56px - 4px); //uniapp内部写死
    overflow: auto;
  }
</style>
```
tree-node.vue
```html
<template>
  <view class="tree-node-box">
    <view
      v-for="(item, index) in props.data"
      :key="index"
      class="tree-node-level"
      :class="{ 'is-expanded': item._expanded }"
    >
      <view
        class="tree-node-item"
        @click="clickNode(item)"
        :class="['level-' + level, { 'is-checked': item._checked }]"
      >
        <view class="tree-node-item__block" :style="{ width: `${(level - 1) * 48}rpx` }"></view>
        <view
          v-if="item.children && item.children.length"
          class="tree-node-item__expand-icon"
          @click.stop="handleOpenClose(item)"
        >
          <sl-icon
            :name="item._expanded ? 'icon_arrow_expanded' : 'icon_arrow_folded'"
            color="primary"
            :size="32"
          />
        </view>
        <view v-else class="tree-node-item__space-icon"></view>
        <view class="tree-node-item__content">
          <slot name="default" :node="item">
            {{ item[props.labelKey] }}
          </slot>
        </view>
        <sl-icon
          class="tree-node-item__checked-icon"
          v-if="item._checked"
          color="primary"
          name="icon_check"
          :size="36"
        />
      </view>
      <!-- 使用组件本身渲染子项 -->
      <template v-if="item.children && item.children.length">
        <tree-node
          v-show="item._expanded"
          :data="item.children"
          :labelKey="labelKey"
          :idKey="idKey"
          :level="level + 1"
          @click="clickNode"
        >
          <template #default="{ node }">
            <slot :node="node">{{ node[props.labelKey] }}</slot>
          </template>
        </tree-node>
      </template>
    </view>
  </view>
</template>

<script setup lang="ts">
  import TreeNode from './tree-node.vue'; // 引入当前组件

  export interface Props {
    data: any;
    labelKey: string;
    idKey: string;
    level?: number;
  }

  const props = withDefaults(defineProps<Props>(), {
    data: [],
    level: 1,
  });

  const emit = defineEmits<{
    (e: 'click', item: any): void;
  }>();
  // 选中
  function clickNode(item: any) {
    emit('click', item);
  }
  // 处理展开或收起，item, index
  function handleOpenClose(item: any) {
    // 如果不存在_expanded属性就添加该属性。
    if (typeof item._expanded === 'undefined') {
      item._expanded = false;
    }
    item._expanded = !item._expanded;
  }
</script>

<style scoped lang="scss">
  .tree-node-box {
    background: $ui-bg-page-white;
  }
  .tree-node-level {
    position: relative;
    // &.is-expanded{
    //   background: $ui-bg-page-white;
    // }
  }
  .tree-node-item {
    display: flex;
    align-items: center;
    padding: $ui-gap-xs $ui-gap-d $ui-gap-xs 0;
    box-sizing: border-box;
    font-size: 28rpx;
    &.level-1,
    &.level-2 {
      font-size: 32rpx;
    }
    &:not(:last-child) {
      border-bottom: 1rpx solid $ui-color-line-deep;
    }
    &.is-checked {
      background: $ui-color-primary-hover;
      color: $uni-primary;
    }
  }
  .tree-node-item__expand-icon {
    color: $uni-info;
    width: 56rpx;
    height: 56rpx;
    line-height: 56rpx;
    text-align: center;
  }
  .tree-node-item__space-icon {
    width: 10rpx;
    height: 56rpx;
    line-height: 56rpx;
  }
  .tree-node-item__block,
  .tree-node-item__expand-icon,
  .tree-node-item__space-icon{
    flex-shrink: 0;
    flex-grow: 0;
  }
  .tree-node-item__checked-icon {
    color: $uni-primary !important;
  }
  .tree-node-item__content {
    width: 80%;
    flex: 1;
    overflow: hidden;
  }
</style>
```