---
title: 【uniapp】h5水印
published: 2024-05-05
description: ''
image: ''
tags: [vue,uniapp]
category: '前端常见业务'
draft: false 
---
watermark.ts
```js
const VIEW_ID: string = '1.23452384164.123412415';

export default class WatermarkClass {
  canvasText: string = '';
  watermarkView?: PlusNativeObj;
  showCanvas?: boolean = true;
  canvasFontSize: number = uni.upx2px(30);
  canvasTextColor: string = '#c8c8c8';
  globalAlpha: number = 0.3;
  canvasRotate: number = (30 * Math.PI) / 180;
  cosRad: number = Math.cos((30 * Math.PI) / 180);
  sinRad: number = Math.sin((30 * Math.PI) / 180);
  canvasTextAlign: 'left' | 'center' | 'right' = 'left';
  canvasTextBaseline: 'middle' | 'top' | 'bottom' = 'middle';
  constructor(canvasText?: string) {
    this.canvasText = canvasText || '';
    this.initWatermark();
  }
  show(): void {
    // #ifdef APP-PLUS
    plus.nativeObj.View.getViewById(VIEW_ID) !== null &&
      plus.nativeObj.View.getViewById(VIEW_ID).show();
    // #endif

    // #ifdef H5
    if (document.getElementById(VIEW_ID)) {
      document!.getElementById(VIEW_ID).style.visibility = 'visible';
    }
    // #endif
  }
  hide(): void {
    // #ifdef APP-PLUS
    plus.nativeObj.View.getViewById(VIEW_ID) !== null &&
      plus.nativeObj.View.getViewById(VIEW_ID).hide();
    // #endif

    // #ifdef H5
    if (document.getElementById(VIEW_ID)) {
      document!.getElementById(VIEW_ID).style.visibility = 'hidden';
    }
    // #endif
  }
  updateWatermark(canvasText: string): void {
    if (this.canvasText === canvasText) return;

    this.canvasText = canvasText || '';
    // #ifdef APP-PLUS
    this.watermarkView?.reset();
    this.drawAppWaterMark(VIEW_ID);
    // #endif

    // #ifdef H5
    this.drawH5WaterMark(VIEW_ID);
    // #endif
  }
  initWatermark(): void {
    // #ifdef APP-PLUS
    this.drawAppWaterMark(VIEW_ID);
    // #endif

    // #ifdef H5
    this.drawH5WaterMark(VIEW_ID);
    // #endif

    uni.$off('show-watermark');
    uni.$off('hide-watermark');
    uni.$off('set-watermark');

    // 全局监听事件，触发水印显示
    uni.$on('show-watermark', () => {
      this.show();
    });

    // 全局监听事件，触发水印隐藏
    uni.$on('hide-watermark', () => {
      this.hide();
    });
    uni.$on('set-watermark', (canvasText: string) => {
      this.updateWatermark(canvasText);
    });
  }
  drawAppWaterMark(id: string): void {
    // #ifdef APP-PLUS
    plus.nativeObj.View.getViewById(id) && plus.nativeObj.View.getViewById(id).close();
    const cans = uni.createCanvasContext('watermarkCanvas');
    const { width: realWidth } = cans.measureText(this.canvasText);
    const { width, height, x, y } = this.getCanvasSize(realWidth, this.canvasFontSize);
    cans.width = width;
    cans.height = height;
    cans.translate(x, y);
    cans.rotate(-this.canvasRotate);
    cans.setFontSize(this.canvasFontSize);
    cans.setFillStyle(this.canvasTextColor);
    cans.setTextAlign(this.canvasTextAlign);
    cans.setTextBaseline(this.canvasTextBaseline);
    cans.fillText(this.canvasText, 0, 0);
    cans.setGlobalAlpha(this.globalAlpha);
    cans.draw(true, () => {
      uni.canvasToTempFilePath({
        canvasId: 'watermarkCanvas',
        success: (res) => {
          if (!res?.tempFilePath) return;

          const imgList = [];
          const sysInfo = uni.getSystemInfoSync();
          const row = Math.ceil(sysInfo.windowHeight / height); // 水印排列行数
          const colum = Math.ceil(sysInfo.windowWidth / width); // 水印排列列数
          for (let i = 0; i < colum; i++) {
            for (let j = 0; j < row; j++) {
              imgList.push({
                tag: 'img',
                src: res.tempFilePath,
                position: {
                  top: height * i + 'px',
                  left: width * j + 'px',
                  width: width + 'px',
                  height: height + 'px',
                },
              });
            }
          }
          // https://www.html5plus.org/doc/zh_cn/nativeobj.html#plus.nativeObj.View.View
          this.watermarkView = new plus.nativeObj.View(
            id,
            {
              top: '0',
              left: '0',
              right: '0',
              bottom: '0',
            },
            imgList,
          );

          // 拦截View控件的触屏事件,将事件穿透给下一层view
          this.watermarkView?.interceptTouchEvent(false);
          this.watermarkView?.show();
        },
      });
    });
    // #endif
  }

  drawH5WaterMark(id: string): void {
    // #ifdef H5
    let div = document.getElementById(id) as HTMLDivElement;
    if (!div) {
      div = document.createElement('div');
      div.id = id;
      div.style.pointerEvents = 'none';
      div.style.top = '0';
      div.style.left = '20px';
      div.style.bottom = '20px';
      div.style.right = '20px';
      div.style.position = 'fixed';
      div.style.zIndex = '100000';
      document.body.appendChild(div);
    }

    const can: HTMLCanvasElement = document.createElement('canvas');
    const cans: CanvasRenderingContext2D = can.getContext('2d');

    // 单个水印宽、高
    const { width: realWidth } = cans.measureText(this.canvasText);
    const { width, height, x, y } = this.getCanvasSize(realWidth, this.canvasFontSize);
    can.width = width;
    can.height = height;
    cans.translate(x, y);
    cans.globalAlpha = this.globalAlpha;
    cans.rotate(-this.canvasRotate);
    cans.font = this.canvasFontSize + 'px sans-serif';
    cans.fillStyle = this.canvasTextColor;
    cans.textAlign = this.canvasTextAlign;
    cans.textBaseline = this.canvasTextBaseline;
    cans.fillText(this.canvasText, 0, 0);
    div.style.background = `url(${can.toDataURL('image/png')}) ${-x}px top repeat`;
    // #endif
  }

  getCanvasSize(fontWidth: number, fontHeight: number) {
    const recWidth = fontWidth * this.cosRad + fontHeight * this.sinRad;
    const recHeight = fontHeight * this.cosRad + fontWidth * this.sinRad;
    const width = recWidth * 2.5;
    const height = recHeight * 3;
    const x = (width - recWidth) / 2;
    const y = (height + recHeight) / 2;
    return {
      recWidth,
      recHeight,
      width,
      height,
      x,
      y,
    };
  }
}

```
App.vue
```js
onLaunch(() => {
    initWatermark();
  });
  const initWatermark = () => {
    const userInfo = getStorage(USER);
    new WatermarkClass(userInfo?.name);
  };
```
demo.vue
```js
uni.$emit('set-watermark', '水印水印水印');//修改水印
```
![预览图](【uniapp】h5水印-1.png)