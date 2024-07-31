---
title: 表格形表单
published: 2024-05-27
description: 表格，每行为单个表单的表格。可配置：表格列、空文本、全部只读、斑马条纹、是否显示操作列
image: ''
tags: [vue]
category: '前端常见业务'
draft: false 
---
FormTable.vue
```yaml
<template>
  <table
    class="form-table td-center readOnly"
    :class="{'is-striped':isStriped}"
  >
    <colgroup>
      <col
        v-for="(it, i) in cols"
        :key="'cols'+i"
        :class="{'hidden':setColDisplay(it,i)}"
        :style="{width:it.width?it.width:''}"
      >
    </colgroup>
    <thead>
      <tr>
        <th
          v-for="(it, i) in cols"
          :key="'header'+i"
          align="center"
          :class="{'hidden':setColDisplay(it,i)}"
        >
          <td>
            {{ it.label }}
          </td>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-if="datas.length===0"
        class="tr-empty"
      >
        <td
          class="tr-empty-panel"
          :colspan="cols.length"
        >
          {{ emptyText }}
          <slot name="formtable-empty" />
        </td>
      </tr>
      <template v-else>
        <tr
          v-for="(item, index) in datas"
          :key="index"
        >
          <td
            v-for="(it, i) in cols"
            :key="i"
            :class="{'opera-td':!it.prop,'hidden':setColDisplay(it,i)}"
          >
            <div class="form-table-cell">
              <el-input
                v-if="it.prop"
                :key="i"
                v-model="item[it.prop]"
                :maxlength="it.maxlength"
                :show-word-limit="it.wordCount"
                :readonly="readonlyArr[index]"
                :class="{'is-error':errorArr[index*colsLength+i]}"
                @blur="handleInputTrim(item[it.prop],index,it.prop);validate(item[it.prop],it.validateReg,index,i);required(item[it.prop],it.required,index,i);"
              />
              <!-- 操作列 -->
              <!-- v-else-if="showOperaCol" -->
              <template v-else>
                <slot
                  name="opera"
                  :scope="{item,index}"
                />
                <div
                  v-if="readonlyArr[index]"
                  class="opera-box"
                >
                  <el-button
                    type="primary"
                    plain
                    @click="editRow(item,index)"
                  >
                    编辑
                  </el-button>
                </div>
                <div
                  v-else
                  class="opera-box"
                >
                  <el-button
                    type="primary"
                    plain
                    @click="save(item,index)"
                  >
                    保存
                  </el-button>
                </div>
                <div
                  v-if="readonlyArr[index]"
                  class="opera-box"
                >
                  <el-button
                    type="text"
                    class="text-danger"
                    @click="deleRow(item,index)"
                  >
                    删除
                  </el-button>
                </div>
                <div
                  v-else
                  class="opera-box"
                >
                  <el-button
                    type="text"
                    class="text-danger"
                    @click="cancel(item,index)"
                  >
                    取消
                  </el-button>
                </div>
              </template>
              <slot :scope="{item,index}" />
            </div>
          </td>
        </tr>
      </template>
    </tbody>
  </table>
</template>

<script>
export default {
  name: 'FormTable',
  model: {
    prop: 'datas',
    event: 'newData'
  },
  props: {
    // 表格列配置
    cols: {
      type: Array,
      default: ()=>[],
      require: true,
    },
    // 表格数据
    datas: {
      type: Array,
      default: ()=>[],
      require: true,
    },
    // 空文本
    emptyText: {
      type: String,
      default: ()=>'暂无数据',
    },
    // 全部只读
    allReadonly: {
      type: Boolean,
      default: true
    },
    // 斑马条纹
    isStriped: {
      type: Boolean,
      default: false
    },
    // 是否显示操作列
    showOperaCol: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      // 只读状态列表，以行为单元
      readonlyArr: [],
      // 校验出错列表，一维数组，以表单项为单元
      errorArr: [],
    };
  },
  computed: {
    colsLength() {
      return this.cols.length - 1;// 最后一列是操作列
    }
  },
  watch: {
    allReadonly: {
      immediate: true,
      handler(val) {
        this.readonlyArr = Array(this.datas.length).fill(val ? 'readonly' : false);
      },
    },
  },
  mounted() {
    this.initForm(); 
  },
  methods: {
    // 初始化: 全部只读，全部校验通过
    initForm() {
      this.readonlyArr = Array(this.datas.length).fill('readonly');
      this.errorArr = Array(this.datas.length * this.colsLength).fill(false);
    },
    // 编辑
    editRow(item,index) {
      // 设置编辑状态
      this.$set(this.readonlyArr,index,false);
      this.$emit('edit',item,index);
    },
    // 保存
    save(item,index) {
      let flag = this.validateLine(index);
      if(!flag) return;
      // 恢复只读状态
      this.$set(this.readonlyArr,index,'readonly');
      this.$emit('save',item,index);
    },
    // 取消
    cancel(item,index) {
      let flag = this.validateLine(index);
      if(flag) {
        // 恢复只读状态
        this.$set(this.readonlyArr,index,'readonly');
      }
      this.$emit('cancel',item,index);
    },
    // 删除行
    deleRow(item,index) {
      this.$emit('delete',item,index);
    },
    // 父组件删除行，需要移除更新required和error信息
    handleAfterOutDelete(lineIndex) {
      if(lineIndex === undefined) return console.error('请指明删除行所在下标');

      this.readonlyArr.splice(lineIndex,1);
      const startIndex = lineIndex * this.colsLength;
      this.errorArr.splice(startIndex,this.colsLength);
    },
    // 校验输入
    validate(value,validateReg,lineIndex,colIndex) {
      if(!this.cols || validateReg === undefined || validateReg instanceof RegExp === false) return;

      let flag = validateReg.test(value);
      let index = lineIndex * this.colsLength + colIndex;
      this.$set(this.errorArr,index,!flag);
    },
    // 处理输入框的首尾空格
    handleInputTrim(value,index,prop) {
      if(!value) return;
      
      this.$set(this.datas[index],prop,value.replace(/^\s+|\s+$/gm, ''));
    },
    // 处理非空
    required(value,required,lineIndex,colIndex) {
      if(!this.cols || !required) return;

      let flag = !value && required;
      let index = lineIndex * this.colsLength + colIndex;
      this.$set(this.errorArr,index,flag);
    },
    // 判断是否整行有误
    isLineError(index) {
      let errorList = this.errorArr.slice(index * this.colsLength - 1,index * this.colsLength + this.colsLength);
      let flag = errorList.includes(true);
      return flag;
    },
    // 设置整行校验错误
    setLineError(index,isError = true) {
      for(let i = 0;i < this.colsLength;i++) {
        let errIndex = index * this.colsLength + i;
        this.$set(this.errorArr,errIndex,isError);
      }
    },
    /**
     * @description: 校验整行数据，并设置校验效果
     * @param {*} index 数据行所在下标
     * @return {*} true校验成功，false校验失败
     */    
    validateLine(index) {
      let item = this.datas[index];// 整行的数据
      // 新增数据导致当前行没有校验信息
      if(this.datas.length * this.colsLength > this.errorArr.length) {
        let newError = Array(this.colsLength).fill(false);
        this.errorArr.push(...newError);
      }
      this.cols.forEach((config,colIndex) => {
        let prop = config['prop'] || null;
        let validateReg = config['validateReg'] || null;
        let required = config['required'] || null;
        if(prop) {
          this.validate(item[prop],validateReg,index,colIndex);
          this.required(item[prop],required,index,colIndex);
        }
      });
      let flag = this.isLineError(index);
      return !flag;
    },
    // 控制操作列的显隐
    setColDisplay(it,i) {
      return i === this.colsLength && !this.showOperaCol;
    }
  }
};
</script>

<style lang='less' scoped>
  @import './FormTable.less';
</style>
```

FormTable.less
```yaml
/* 涉及到的外部变量(在@/assets/styles/variables.less中):
   @font-default, @font-danger
   @table-bg, @table-thead-color, @table-thead-bg, @table-border, @table-tr-odd, @table-tr-even, @tr-hover
   @th-font-size, @td-font-size, @tr-space, @td-space
   @padding-input
*/
.form-table {
    /* 变量命名 */
    @opera-num: 2; //操作列的列数
    @padding-input: 5px 15px; //输入框的间距

    border-collapse: collapse;
    width: 100%;
    color: @font-default;
    background-color: @table-bg;

    /* 空白行 */
    .tr-empty .tr-empty-panel {
        text-align: center;
        padding: 20px;
    }

    /* 文本对齐方式 */
    &.td-center {
        tbody tr td {
            text-align: center;
        }
    }

    &.td-left {
        tbody tr td {
            text-align: left;
        }
    }

    &.td-right {
        tbody tr td {
            text-align: right;
        }
    }

    /* 表头样式 */
    thead {
        color: @table-thead-color;
        background-color: @table-thead-bg;
        font-size: @th-font-size;
    }

    /* 单元格间距 */
    thead tr th {
        padding: @tr-space;

        td {
            padding: @padding-input;
        }
    }

    tbody tr td .form-table-cell {
        display: inline-block;
        box-sizing: border-box;
        position: relative;
        vertical-align: middle;
        width: 100%;
        padding: @td-space;
    }

    /* 绘制边框 */
    thead tr,
    tbody tr {
        border-top: @table-border;
        border-right: @table-border;
    }

    thead tr th,
    tbody tr td {
        &:not(:last-child) {
            border-right: @table-border;
        }
    }

    thead tr th:first-child,
    tbody tr td:first-child {
        border-left: @table-border;
    }

    tbody tr:last-child {
        border-bottom: @table-border;
    }

    &.is-striped {
        tr {
            background-color: @table-tr-odd;
        }

        // 偶数行
        tr:nth-child(even) {
            background-color: @table-tr-even;
        }
    }

    tbody {
        font-size: @td-font-size;

        // 鼠标悬浮的颜色
        tr:hover {
            background-color: @tr-hover;
        }
    }

    .opera-td {
        position: relative;
        min-width: 80px;

        .form-table-cell {
            width: 100%;
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            display: grid;
            grid-template-columns: repeat(@opera-num, calc(100% / @opera-num));
            justify-items: center;
            align-items: center;
            padding: 0;

            .opera-box {
                width: 100%;
                height: 100%;
                padding: @td-space;
                display: flex;
                align-items: center;
                justify-content: center;

                /deep/.el-button {
                    font-size: @td-font-size;
                    border-color: transparent;
                    background: unset;

                    &:hover {
                        background-color: inherit;
                    }
                }
            }

            .opera-box+.opera-box {
                border-left: @table-border;
            }
        }
    }

    /deep/input {
        text-align: left;
        transition: all 0.5s;
        font-size: @td-font-size;
        padding: @padding-input;
    }

    /deep/input[readonly="readonly"] {
        border-color: transparent;
        background: unset;
        cursor: default;
        text-align: center;
    }

    /deep/.el-input.is-error {
        .el-input__inner {
            border-color: @font-danger;
        }
    }
}

/deep/.hidden {
    display: none;
}
```

demo.vue
```yaml
<template>
  <div class="layout-colsumn detail-panel">
    <div class="page-opera">
      <el-button
        type="primary"
        @click="add"
      >
        新增
      </el-button>
      <el-button
        v-if="readOnly"
        type="primary"
        @click="readOnly=!readOnly"
      >
        编辑
      </el-button>
      <el-button
        v-else
        type="primary"
        @click="cancel"
      >
        取消
      </el-button>
    </div>
    <!-- 人员信息 -->
    <form-table
      ref="form-table"
      v-model="list"
      v-loading="loading"
      :datas="list"
      :cols="cols"
      :all-readonly="readOnly"
      :show-opera-cols="true"
      :empty-text="null"
      @save="handleRow"
      @delete="deleteRow"
      @cancel="cancelRow"
    >
      <template #formtable-empty>
        <span
          class="cursor"
          @click="getList"
        >暂无数据，点击刷新</span>
      </template>
    </form-table>
    <pagination
      class="page-area"
      :page.sync="pageInfo.current"
      :page-sizes="[10, 20, 40, 60,120]"
      :limit.sync="pageInfo.size"
      :total="total"
      @pagination="getList"
    />
  </div>
</template>

<script>
import Pagination from '@/components/pagination/index';// 分页
import FormTable from '@/components/form-table/FormTable';// 内嵌表单的表格

export default {
  name: 'ShipDetail',
  components: {
    Pagination, // 分页
    FormTable,// 内嵌表单的表格
  },
  data() {
    return {
      readOnly: true,// 状态 人员表只读
      pageInfo: {
        current: 1,
        size: 10,
      },
      // 人员表配置
      cols: [
        { prop: 'personName',label: '姓名',required: true,wordCount: true,maxlength: 32},
        { prop: 'idcard',label: '身份证',required: true,wordCount: true,maxlength: 32},
        { label: '操作'}
      ],
      list: [],//  人员表单数据
      total: 0,// 人员信息总数
      loading: false,// 人员 信息加载状态
    };
  },
  mounted() {
    this.getList();
  },
  methods: {
    // 获取人员信息
    async getList() {
      // ...
      // this.list = res.result.records || [];
      // this.total = res.result.total;
      this.$nextTick(()=>{
        this.$refs['form-table'].initForm();
      });
    },
    // 点击新增人员信息按钮
    add() {
      let tmp = {personName: null,idcard: null};
      this.list.push(tmp);
    },
    // 取消新增人员按钮
    async cancel() {
      await this.getList();// 先获取全新的数据，再控制显隐
      this.readOnly = !this.readOnly;
    },
    // 人员行内取消按钮
    cancelRow(item,index) {
      // ...
      this.list.splice(index,1);
      this.$refs['form-table'].handleAfterOutDelete(index);
    },
    // 删除人员信息
    deleteRow(item,index) {
      this.$beforeDelete('确认删除该人员？',async ()=>{
        // ...api
        this.list.splice(index,1);
        this.total = this.total - 1;
        this.$refs['form-table'].handleAfterOutDelete(index);
      });
    },
    // 处理人员的保存和修改
    async handleRow(item,index) {
      // ...
    },
  }
};
</script>
```