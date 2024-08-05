---
title: 地图工具类：多边形
published: 2024-04-30
description: '基于openlayers的多边形，多用于绘制网格区域'
image: ''
tags: [js]
category: '前端常见业务'
draft: false 
---
```js
import { Vector as SourceVector } from 'ol/source';
import { Feature } from 'ol';
import { Style, Stroke, Fill, Text } from 'ol/style';
import { Vector as LayerVector } from 'ol/layer';
import { Polygon,GeometryCollection } from 'ol/geom';

export default class OlMapGrid {
  constructor(mapObj) {
    this.mapObj = mapObj;
    this.idOfGrid = {};
    this.colors = [
      {strokeColor: '#007CEE',fillColor: '#007CED1A',fillOpacity: 1,titleFillColor: '#4A91E8'},
      {strokeColor: '#0DA25A',fillColor: '#0DA25A1A',fillOpacity: 1,titleFillColor: '#0DA25A'},
      {strokeColor: '#F1A31B',fillColor: '#F1A31B1A',fillOpacity: 1,titleFillColor: '#F1A31B'},
      {strokeColor: '#ff41df',fillColor: '#ff41df1A',fillOpacity: 1,titleFillColor: '#ff41df'},
    ];
    this.selectRowId = '';
    this.prevSelectLayer = []; // 上个选中图层
  }
  /**
     * 显示网格
     * @param {*} pointsArr             [ [[经度, 纬度], [经度, 纬度]] ], 即:[网格分区1,网格分区2]
     * @param {*} id                  折线id 用于清除面
     * @param {*} isShowTitle         是否显示标题
     * @param {*} title               标题
     * @param {*} strokeColor         颜色
     * @param {*} strokeWidth         折线宽度
     * @param {*} fillColor           面填充色
     * @param {*} fillOpacity         面填充色的透明度
     * @param {*} titleFillColor      标题背景填充色
     * @param {*} zIndex              图层显示级别
     * @param {*} isSole : 是否唯一 默认false
     * @param {*} isAutoColor : 是否自动处理配色，如是则相关颜色优先级高于传参 默认false
     */
  showGrid({
    pointsArr,
    id = new Date().getTime(),
    isShowTitle = false,
    title = '',
    strokeColor,
    strokeWidth,
    fillColor,
    fillOpacity = 0.6,
    titleFillColor,
    zIndex = 100,
    isSole = false,
    isAutoColor = false,
  } = {}) {
    if (!this.mapObj) return;

    if (isSole) {
      this.clearGridById(id);
    }

    // 没有点位信息, 不做处理
    if (!pointsArr.length) return;
    
    // 创建多个特征
    const features = pointsArr.map((points, index) => {
      const coordinates = points.map((item)=>{
        // 是数组:将经纬度转为Number类型(否则标题不能正常显示)
        if(Array.isArray(item)) {
          return [+item[0], +item[1]];
        }
        // 是对象
        const longitude = item.longitude || item.long;
        const latitude = item.latitude || item.latitude;
        return [+longitude, +latitude];
      });

      return new Feature({
        name: `grid-${id}-${index}`,
        _gridId: id,
        _index: index,
        geometry: new Polygon([coordinates])
      });
    });
    // 创建一个 source图层, 把features添加到source里
    const source = new SourceVector();
    source.addFeatures(features);

    // 处理默认颜色
    if(isAutoColor) {
      const colorConfig = this.getGridColor();
      strokeColor = colorConfig.strokeColor;
      fillColor = colorConfig.fillColor;
      fillOpacity = colorConfig.fillOpacity;
      titleFillColor = colorConfig.titleFillColor;
    }
    const gridLayer = new LayerVector({
      className: 'map-grid',
      source: source,
      zIndex: zIndex,
      style: this.getGirdStyle({strokeColor,strokeWidth,fillColor,titleFillColor,isShowTitle,title}),
      opacity: fillOpacity
    });
    gridLayer.set('_colorConfig',{
      strokeColor,
      strokeWidth,
      fillColor,
      titleFillColor
    });
    gridLayer.set('_titleConfig',{
      isShowTitle,
      title,
    });
    this.idOfGrid[id] = {
      layer: gridLayer,
      features: features
    };
    this.mapObj.addLayer(gridLayer);
  }
  /**
     * 根据id修改多边形颜色
     * @param {*} id
     * @param {*} color
     * @returns
     */
  updateGridColorById(id, color, strokeColor) {
    if (!this.idOfGrid[id]) return;

    let { layer } = this.idOfGrid[id];
    layer.getStyle().setFill(
      new Fill({
        color
      })
    );
    layer.getStyle().setStroke(
      new Stroke({
        color: strokeColor,
        width: 2
      })
    );
    layer.changed();
  }
  /* 网格-根据id清除 */ 
  clearGridById(id) {
    if (!this.idOfGrid[id]) return;

    let { layer } = this.idOfGrid[id];
    layer.getSource().clear();
    this.mapObj.removeLayer(layer);
    layer = null;
    delete this.idOfGrid[id];
  }
  /* 网格-清除所有 */ 
  clearAllGrid() {
    Object.keys(this.idOfGrid).forEach((key) => {
      this.clearGridById(key);
    });

    this.idOfGrid = {};
  }
  /* 网格-获取配色信息 */ 
  getGridColor() {
    const currentGridLength = Object.keys(this.idOfGrid).length;
    const nextColorIndex = currentGridLength % (this.colors.length);
    return this.colors[nextColorIndex];
  }
  /* 网格-获取区域中心点 */ 
  getGridCenter(id) {
    let { features } = this.idOfGrid[id] || {};
    if(!features) return null;

    const geometryCollection = new GeometryCollection(features.map((feature)=>feature.getGeometry()));
    const center = geometryCollection.getClosestPoint(geometryCollection.getExtent());
    return center;
  }

  /** hover左边菜单 */
  onEnterLayer(rowId) {
    const {layer} = this.idOfGrid[rowId] || {};
    if(!layer)return;

    const titleInfo = layer.get('_titleConfig');
    layer.setStyle(this.getGirdStyle(titleInfo));
  }

  /** 离开左边菜单 */
  onLeaveLayer(rowId) {
    const {layer} = this.idOfGrid[rowId] || {};
    if(!layer)return;
    
    const titleInfo = layer.get('_titleConfig');
    const colorInfo = layer.get('_colorConfig');
    layer.setStyle(this.getGirdStyle({...colorInfo,...titleInfo}));
  }

  /** 获取网格样式配置 */
  getGirdStyle({strokeColor = '#ff0000',strokeWidth = 2,fillColor = '#ff00005C',titleFillColor = '#ff0000',isShowTitle,title}) {
    return new Style({
      // 多边形边框
      stroke: new Stroke({
        color: strokeColor,
        width: strokeWidth
      }),
      // 多边形填充样式
      fill: new Fill({
        color: fillColor
      }),
      // 文本样式
      text: isShowTitle
        ? new Text({
          text: title,
          fill: new Fill({
            color: '#fff'
          }),
          font: '14px Microsoft YaHei',
          offsetX: 0,
          offsetY: 0,
          padding: [4, 12, 4, 12],
          backgroundFill: new Fill({
            color: titleFillColor
          })
        })
        : null
    });
  }
};
```