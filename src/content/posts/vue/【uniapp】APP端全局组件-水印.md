---
title: 【uniapp】APP端全局组件-水印
published: 2024-03-15
description: 'APP端基于文件缓存的水印组件，及自定义组件配置到全局方法'
image: ''
tags: [vue,uniapp]
category: '前端常见业务'
draft: false 
---
## 一、水印核心代码
```html
<template>
  <view
    class="watermark-box"
    :style="{backgroundImage:`url(${waterMarkImgSrc})`,backgroundSize:`auto ${height}px`}"
  >
    <canvas
      id="watermark"
      class="watermark"
      canvas-id="watermark"
    />
  </view>
</template>

<script>
// 字体大小
const fontSize = 14;
// 倾斜角：建议取（0，90），该区间的sin及cos为正值，省去三角函数取绝对值的计算
const rotateRad = 30 * Math.PI / 180;
const cosRad = Math.cos(rotateRad);
const sinRad = Math.sin(rotateRad);

export default {
  name: 'WaterMark',
  data() {
    return {
      waterMarkImgSrc: '',
      width: '',
      height: ''
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    // 水印初始化
    init() {
      const {filePath,width,height} = JSON.parse(uni.getStorageSync('watermarkImg') || '{}');
      // 获取文件路径，读取文件。成功则使用缓存图片，失败或缓存不存在则生成水印
      if(filePath) {
        const path = plus.io.convertLocalFileSystemURL(filePath);
        uni.getImageInfo({
          src: path,
          success: () => {
            this.waterMarkImgSrc = 'file://' + path;
            this.width = width;
            this.height = height;
          },
          fail: ()=>{
            this.systemSetWaterMark();
          }
        });
      }else{
        this.systemSetWaterMark();
      }
    },
    // 水印文字
    getStr() {
      return '水印';
    },
    getCanvasSize(fontWidth,fontHeight) {
      const recWidth = Math.ceil(fontWidth * cosRad + fontHeight * sinRad);
      const recHeight = Math.ceil(fontHeight * cosRad + fontWidth * sinRad);
      const width = parseInt(recWidth * 2);
      const height = parseInt(recHeight * 2.5);
      const x = parseInt((width - recWidth) / 2);
      const y = parseInt((height + recHeight) / 2);
      return {
        recWidth,
        recHeight,
        width,
        height,
        x,
        y,
      };
    },
    // 生成系统水印
    systemSetWaterMark() {
      // #ifdef APP-PLUS
      const watermarkStr = this.getStr();
      const ctx = uni.createCanvasContext('watermark');
      // 单个水印宽、高
      const {width: fontRealWidth} = ctx.measureText(watermarkStr);
      const { width, height, x, y } = this.getCanvasSize(fontRealWidth,fontSize);
      // 水印倾斜后的宽度、高度
      this.width = width;
      this.height = height;
      // 设置画布的长宽
      ctx.width = this.width;
      ctx.height = this.height;
      // 透明度
      ctx.setGlobalAlpha(0.3);
      // 字体大小
      ctx.setFontSize(fontSize);
      // 设置填充绘画的颜色、渐变或者模式
      ctx.setFillStyle('#c8c8c8');
      // 中心偏移（由左上角改为中心
      cans.translate(x, y);
      // 设置旋转角度：带负号为逆时针
      ctx.rotate(-rotateRad);
      // 设置文本内容的当前对齐方式
      ctx.textAlign = 'center';
      // 设置在绘制文本时使用的当前文本基线
      ctx.textBaseline = 'Middle';
      // 在画布上绘制填色的文本（输出的文本，开始绘制文本的X坐标位置，开始绘制文本的Y坐标位置）
      ctx.fillText(watermarkStr,0,0);
      ctx.draw(true,() => {
        // 绘制完成后生成图片，缓存相对图片路径及图片宽高
        uni.canvasToTempFilePath({
          canvasId: 'watermark',
          width: this.width,
          height: this.height,
          quality: 1,
          fileType: 'png',
          success: (res) =>{
            const path = plus.io.convertLocalFileSystemURL(res.tempFilePath);
            const data = {
              filePath: res.tempFilePath,// 相对路径
              width: this.width,
              height: this.height
            };
            uni.setStorageSync(USER_MASK,JSON.stringify(data));  
            this.waterMarkImgSrc = 'file://' + path;  
          }
        });
      });
      // #endif
    }
  }
};
</script>

<style lang='scss' scoped>
.watermark-box{
  overflow: hidden;
  width: 100%;
  height: 100vh;
  pointer-events: none;
  position: fixed;
  top: 30px;
  z-index: 9999;
  left: 0;
  top: 0;
  background-repeat: repeat;
}
.watermark{
  z-index: -1;
}
</style>
```

## 二、配置全局自定义组件
**水印组件路径：**
`@/pages/components/watermark/index.vue`
**pages.json中配置easycom**
```json
{
	"easycom": {
		"autoscan": false,
		"custom": {
			"^u-(.*)": "@/uni_modules/uview-ui/components/u-$1/u-$1.vue",
			"^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue",
			"^g-(.*)": "@/pages/components/$1/index.vue"
		}
	},
	"pages": []
}
```

**使用**
`<g-watermark />`

