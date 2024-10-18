---
title: 地图工具类：点位
published: 2024-04-30
description: '基于openlayers的点位，包括聚合点、高亮'
image: ''
tags: [openlayers,js]
category: '前端常见业务'
draft: false 
---

```js
import 'ol/ol.css';
import Overlay from 'ol/Overlay';
import { Vector as SourceVector, Cluster } from 'ol/source';
import { Feature } from 'ol';
import { Style, Fill, Text, Icon, Circle, Stroke } from 'ol/style';
import { Vector as LayerVector } from 'ol/layer';
import { Point } from 'ol/geom';

export default class OlMapPoint {
  constructor(mapObj) {
    this.mapObj = mapObj;
    this.typeOfLayers = {};
    this.typeOfOverlays = {};
    this.typeOfHightLightFeatureIds = {};
  }

  /**
     * 显示地图标注
     * @param {*} list : { 经度：longitude/lon/lng, 纬度：latitude/lat, }
     * @param {*} type : 标注类型，可用于清除该类的标注
     * @param {*} iconUrl : 图标的远端url
     * @param {*} icon : 标注的图标 - 从项目中获取
     * @param {*} activeIcon: 高亮图标
     * @param {*} iconSize: 图标大小 [40, 40]
     * @param {*} isSole : 是否唯一 默认false
     * @param {*} isCluster : 是否聚合图标
     * @param {*} isClickCenter : 图标点击后是否居中
     * @param {*} isHightlight : 图标是否高亮
     * @param {*} titleKey : 映射list组数对象中的字段,用于展示标注标题，默认title
     * @param {*} isShowTitle : 是否显示标题，默认false
     * @param {*} idKey : 映射list组数对象中的字段,用于获取marker，进行位置信息修改，默认id
     * @param {*} activeColor : 聚合点颜色
     * @returns
     */
  showPoints({
    list,
    type,
    icon = 'default',
    activeIcon = '',
    iconUrl,
    iconSize = [40, 40],
    idKey = 'id',
    titleKey = 'title',
    isCluster = false,
    isClickCenter = false,
    isSole = false,
    isShowTitle = false,
    activeColor = '',
    zIndex = 200
  }) {
    if (isSole) {
      this.clearPointsByType(type);
    }
    if (!this.typeOfLayers[type]) {
      this.typeOfLayers[type] = {};
    }
    let features = [];
    let activeIconImg = '';
    if (activeIcon) {
      activeIconImg = require(`../images/${activeIcon}`);
    }
    // 点击事件
    const isClick = isClickCenter;
    const featureClickFun = ({ data, pointType }) => {
      if (isClickCenter) {
        this.mapObj.getView().setCenter(data._lonLat);
      }
    };

    // 图标
    const getIcon = (item, index)=>{
      if(iconUrl) {
        return iconUrl.constructor === Function ? iconUrl(item, index) : iconUrl;
      }

      if(icon) {
        return icon.constructor === Function ? icon(item, index) : require(`../images/${icon}`);
      }

      require('../images/default.png');
    };
    // 处理列表数据
    list.forEach((item, index) => {
      let lon = +item.longitude || +item.lon || +item.lng;
      let lat = +item.latitude || +item.lat;
      const _lonLat = [lon, lat];
      const id = item[idKey] || undefined;
      const _iconImg = getIcon(item, index);
      // 创建图标特性
      const iconFeature = new Feature({
        type: 'icon',
        name: id || 'name',
        id,
        geometry: new Point(_lonLat),
        iconImg: _iconImg,
        activeIconImg,
        _data: {
          ...item,
          _id: id,
          _title: item[titleKey || 'title'] || '',
          _iconSize: iconSize,
          _lonLat: _lonLat
        },
        _type: type,
        _activeColor: activeColor,
        _showTitle: isShowTitle,
      });
      if (isClick) {
        iconFeature.addEventListener('click', featureClickFun);
      }
      features.push(iconFeature);
    });

    let layer;
    // 聚合图层
    if (isCluster) {
      layer = this.createCluterLayer({
        features,
        zIndex
      });
    } else {
      const vectorSource = new SourceVector({
        features: features
      });
        // 创建矢量层
      layer = new LayerVector({
        className: 'map-icon-mark',
        source: vectorSource,
        zIndex: zIndex,
        // 创建图标样式
        style: (feature)=>this.getSingleStyleByFeature(feature)
      });
    }
    this.typeOfLayers[type] = {
      layer,
      features,
      featureClickFun
    };
    this.mapObj.addLayer(layer);
  }

  clearPointsByType(type) {
    if (!this.typeOfLayers[type]) return;

    let { layer, features, featureClickFun } = this.typeOfLayers[type];
    // 移除点击事件
    features.forEach((feature) => {
      feature.removeEventListener('click', featureClickFun);
    });
    layer && this.mapObj.removeLayer(layer);
    this.removePopupById(type);
    delete this.typeOfLayers[type];
    delete this.typeOfHightLightFeatureIds[type];
  }

  // 移除全部标注
  clearAllPoints() {
    Object.keys(this.typeOfLayers).forEach((type) => {
      this.clearPointsByType(type);
    });
    this.typeOfLayers = {};
  }

  // 更新标注经纬度
  updateLonlat(type, id, lonLat) {
    if (!this.typeOfLayers[type]) return;

    let { features } = this.typeOfLayers[type];
    let updateFeature = features.find((feature) => feature.get('id') === id);
    if (!updateFeature) return;

    updateFeature.setGeometry(new Point(lonLat));
    updateFeature.changed();
  }

  /**
 * 创建气泡
 * @param {String} id : 气泡唯一标识，用于清除该类的气泡
 * @param {Document} el : DOM元素
 * @param {Object} data : 相关气泡信息，如有longitude、latitude，显示时会默认显示
 * @param {*} args mapPopup相关参数
 */
  createPopup({id,el,data = null,...args}) {
    // this.removePopupById(id);
    const overlayer = mapPopup({id,el,...args});
    this.typeOfOverlays[id] = {
      id,
      overlayer,
      data
    };
    this.mapObj.addOverlay(overlayer);
  }

  /**
 * 显示气泡图层
 * @param {String} id : 气泡唯一标识
 * @param {Array} lonLat : [longitude,latitude]
 */
  showPopupById(id,lonLat) {
    let {data,overlayer} = this.typeOfOverlays[id] || {};
    if(!overlayer)return;

    let {longitude,latitude} = data || {};
    if(!lonLat?.length && longitude && latitude) {
      lonLat = [longitude,latitude];
    }
    if(!lonLat?.length) {
      throw new Error('气泡经纬度信息不能为空');
    }
    overlayer.setPosition(lonLat);
    overlayer.setVisible(true);
  }
  /**
 * 隐藏气泡图层
 * @param {String} id : 气泡唯一标识
 */
  hidePopupById(id) {
    let {overlayer} = this.typeOfOverlays[id] || {};
    if(!overlayer)return;

    overlayer.setVisible(false);
    overlayer.setPosition(undefined);// 不加此行会导致拖动地图时，气泡再次渲染
  }
  /**
 * 删除气泡图层
 * @param {String} id : 气泡唯一标识
 */
  removePopupById(id) {
    if(!this.typeOfOverlays[id]) return;

    let {overlayer} = this.typeOfOverlays[id] || {};
    overlayer.setVisible(false);
    this.mapObj.removeOverlay(overlayer);
    delete this.typeOfOverlays[id];
  }

  /** 高亮标注:根据类型和特征点id集，设置高亮状态, 返回当前高亮id集
   * @param {Object} param0
   * @param {String} param0.type 类型
   * @param {Array} param0.selectIds 选中项的id集合
   * @param {Array} param0.deselectIds 取消选中项的id集合
   * @param {Boolean} param0.isMuti 是否多选
   */
  setHighlightStatus({type,selectIds,deselectIds,isMuti = false}) {
    if(!this.typeOfHightLightFeatureIds[type]) {
      this.typeOfHightLightFeatureIds[type] = [];
    };

    if(isMuti) {
      this.typeOfHightLightFeatureIds[type] = this.typeOfHightLightFeatureIds[type].filter((id)=>deselectIds.includes(id)).concat(selectIds);
    }else{
      this.typeOfHightLightFeatureIds[type] = selectIds;
    }
    const {layer} = this.typeOfLayers[type] || {};
    if(!layer) return;
    
    layer.getSource().changed();
  }

  /**
 * 根据类型和坐标, 获取features
 * @param {String} type : 类型
 * @param {Array} coordinate : 坐标
 */
  getFeatureByTypeAtCoordinate(type,coordinate) {
    if(!type || !coordinate) return;

    const {layer,features} = this.typeOfLayers[type] || {};
    if(!layer || !features) return;

    return features.filter(item=>{
      const lonLat = item.get('_data')._lonLat;
      return lonLat[0] === coordinate[0] && lonLat[1] === coordinate[1] ;
    });
  }

  /* 点位样式-单点 */
  getSingleStyleByFeature(feature) {
    let iconImg = feature.get('iconImg');
    let data = feature.get('_data');
    let type = feature.get('_type');
    let id = feature.get('id');
    let isHightlight = (this.typeOfHightLightFeatureIds[type] || []).includes(id);
    const isShowTitle = feature.get('_showTitle');
    let lonLat = feature.getGeometry().getFirstCoordinate();
    let title = data._title || lonLat.join(',') || '';
    let iconSize = data._iconSize || [40, 40];
    const scale = isHightlight ? 1.5 : 1;
    return new Style({
      image: new Icon({
        src: iconImg,
        imgSize: iconSize,
        scale
      }),
      // 文本样式
      text: isShowTitle
        ? new Text({
          text: title.length <= 20 ? title : title.substring(0, 20) + '...',
          fill: new Fill({
            color: '#131414'
          }),
          font: '14px Microsoft YaHei',
          fontWeight: isHightlight ? 'bold' : 'normal',
          offsetX: 0,
          offsetY: -parseInt(iconSize[1] / 2 * scale + 16),
          padding: [2, 12, 2, 12],
          backgroundFill: new Fill({
            color: '#FFFFFF'
          }),
          backgroundStroke: new Stroke({
            color: '#FFFFFF',
            width: 10,
            lineCap: 'round',
          })
        })
        : null
    });
  }

  getCluterStyle(feature) {
    const features = feature.get('features');
    const size = features.length;
    if(size <= 0) return;

    const type = features[0].get('_type');
    const activeColor = features[0].get('_activeColor');
    const hightlightIds = this.typeOfHightLightFeatureIds[type] || [];
    const isHightlight = !!features.find((item)=>hightlightIds.includes(item.get('id')));
    // 聚合样式
    if (size > 1) {
      return new Style({
        image: new Circle({
          radius: 16,
          stroke: new Stroke({
            color: 'white'
          }),
          fill: new Fill({
            color: isHightlight ? '#ffff00' : activeColor
          })
        }),
        text: new Text({
          text: size.toString(),
          fill: new Fill({
            color: isHightlight ? '#000' : '#fff'
          }),
          font: '14px Microsoft YaHei',
        })
      });
    } else {
      let _feature = features[0];
      if (!_feature) return;

      return this.getSingleStyleByFeature(_feature);
    }
  }
  /* 点位样式-聚合点 */
  createCluterLayer({
    features,
    zIndex
  }) {
  // // 创建矢量容器
    const vectorSource = new SourceVector({
      features: features
    });
    const clusterSource = new Cluster({
      source: vectorSource,
      distance: 50
    });

    // 创建聚合图层
    let clustersLayer = new LayerVector({
      source: clusterSource,
      visible: true,
      zIndex: zIndex || 200,
      style: (feature)=>this.getCluterStyle(feature)
    });
    return clustersLayer;
  }
}

/* ==================================================================================  配置相关 */

/* 气泡 */
const mapPopup = ({ id = 'popup', el, offset,className }) => {
  return new Overlay({
    id,
    element: el,
    stopEvent: true,
    offset: offset || [0, 0],
    className,
    autoPanAnimation: {
      duration: 250
    }
  });
};

/**
 * 根据经纬度查询特征
 * @param {String} id : 气泡唯一标识
 */
export const getFeatureByLonLat = (mapObj,lonlat)=> {
  if(!lonlat || !mapObj) return;

  // 经纬度坐标转换
  const geometry = new Point(lonlat);
  const coordinates = geometry.getCoordinates();
  // 转换为地图上的像素点
  const pixel = mapObj.getPixelFromCoordinate(coordinates);
  // 查询特征
  const features = mapObj.getFeaturesAtPixel(pixel);
  return features;
};
```