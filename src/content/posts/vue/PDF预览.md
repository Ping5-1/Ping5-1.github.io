---
title: PDF预览组件
published: 2023-03-05
description: ''
image: ''
tags: [vue]
category: '前端常见业务'
draft: false 
---

```html
<template>
  <div class="pdf-preview-dialog">
    <div class="button-panel">
      <el-button
        size="mini"
        @click="closeView"
      >
        返回
      </el-button>
      <el-button
        type="primary"
        size="mini"
        @click="download"
      >
        下载
      </el-button>
    </div>
    <div
      ref="pdfContentRef"
      v-infinite-scroll="handleScroll"
      :infinite-scroll-distance="8"
      :infinite-scroll-disabled="disabled"
      class="pdf-dialog-content"
    >
      <pdf
        v-for="i in currentPage"
        ref="pdf"
        :key="i"
        :page="i"
        class="pdf-box"
        :src="preViewFile.fileUrl"
        @progress="$event=>totalProgress=Math.floor($event*100)"
      />
      <div class="load-pdf-more" />
    </div>
    <el-progress
      v-show="totalProgress!==100&&totalProgress!==0"
      class="progress"
      type="circle"
      :width="70"
      color="#0079e7"
      :percentage="totalProgress"
    />
  </div>
</template>

<script>
import pdf from 'vue-pdf';
export default {
  name: 'PdfView',
  components: {
    pdf
  },
  props: {
 
    preViewFile: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      progress: [],
      totalProgress: 0,
      scale: 100,  //  开始的时候默认和容器一样大即宽高都是100%
      currentPage: 1, // pdf文件页码
      numPages: 0, // pdf文件总页数
      fileShow: true, //
    };
  },
  computed: {
    disabled() {
      return (this.totalProgress !== 100 && this.totalProgress !== 0) || this.currentPage === this.numPages;
    }
  },
  mounted() {
    this.pdfToImg();
  },
  methods: {
    handleScroll(e) {
      if(this.currentPage < this.numPages) {
        this.currentPage++;
      }
    },
    // 当一次性取多个页面时，处理文件加载进度
    handleProgress(i,value) {
      this.progress[i - 1] = value;
      if(!this.numPages) return this.totalProgress = 0;

      const total = _.sum(this.progress) / this.currentPage;
      this.totalProgress = Math.floor(total * 100);
    },
    pdfToImg() {
      if(!this.preViewFile.fileUrl) return;
      this.numPages = 0;
      this.currentPage = 1;
      // 计算pdf页码总数
      let loadingTask = pdf.createLoadingTask(this.preViewFile.fileUrl);
      if(!loadingTask || !loadingTask.promise) return;

      loadingTask.promise.then((pdfInfo)=>{
        this.numPages = pdfInfo.numPages;
      });
    },
    // 关闭返回
    closeView() {
      this.$emit('closeView');
    },
    // 下载文件
    download() {
      if(!this.preViewFile || !this.preViewFile.fileUrl) return;
        
      window.open(this.preViewFile.fileUrl, '_blank');
    },
  }
};
</script>

<style lang='less' scoped>
.pdf-preview-dialog {
    position: fixed;
    z-index:99;
    height: 100vh;
    padding-top: 72px;
    bottom: 0;
    left: 0;
    z-index: 99;
    width: 100%;
    background: #fff;
      .pdf-dialog-content {
        padding: 8px;
        width: 100%;
        height: calc(100vh - 100px);
        overflow-y: auto;
        .webkit-scrollbar(0,0);
        margin:0;
        padding:0;
        .pdf-box{
            width: 100%;
            height: 100%;
        }
        .load-pdf-more {
          height: 12px;
        }
      }
      /deep/.progress{
            position: absolute;
            left: 50%;
            top:50%;
            transform: translate(-50%);
        }
      .button-panel{
        padding: 0 20px;
        display: flex;
      }
      /deep/.el-button--mini{
        padding: 6px 10px;
      }
  }
  .file-preview{
      z-index: 99;
      position: fixed;
      left: 0;
      top: 60px;
      width:100vw;
      height: calc(100vh - 60px);
  }
  .file-btn-close{
      position: absolute;
      height: 60px;
      left: 0;
      top: 0;
      width: 100%;
      padding:0 10px;
      background: #323639;
      display: flex;
      align-items: center;
      gap:10px;
      [class^=el-icon]{
          padding: 4px;
          color: #fff;
          font-size: 20px;
          font-weight: bolder;
      }
  }
  
</style>
```
