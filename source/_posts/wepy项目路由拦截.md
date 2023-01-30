---
title: wepy项目路由拦截
date: 2023-01-06 16:42:32
tags: 微信小程序
---

微信小程序的实习方式和vue的类似，但是对于路由，原生小程序没有路由拦截，需要自己进行处理。以下场景就对小程序的路由拦截做一定的处理。

## 需求
项目进行升级的时候，遇到一个需求，在升级期间，用户点击所有功能，都要提示：系统维护中。当前小程序使用的框架是wepy。

## 分析
### 用户进入小程序的场景：
- 最近使用的小程序/我的小程序
- 搜索
- 公众号菜单栏
- 扫描小程序二维码

### 需要处理的关键点
- 点击图标进入指定页面，主要使用api：navigateTo, reLaunch
- 只允许用户进入tabbar页面

## 方案
根据上面的分析，
- 只允许用户进入tabber页面，就需要在某些指定页面进行页面拦截，当用户已进入这个页面，就停止这个页面的事件，并跳转回tabbar页面，此时需要使用switchTab方法
- tabbar页面点击图标/按钮，需要跳转到别的页面（主要使用api：navigateTo, reLaunch）的时候，拦截，拦截，并弹出提示

### 方案-：原生小程序的拦截
``` bash
// /utils/pageFilter.js
function routerFilter(_page){
    if(_page && _page.onLoad){
        const _onLoad = _page.onLoad;
        _page.onLoad = function(e){
            const isCHecked = wx.getStorageSync('isChecked');
            if(isCHecked){
                const pages = getCurrentPages();
                const currentPage = pages[pages.length-1];
                _onLoad.call(currentPage, e);
            }
        } else {
            wx.reLaunch({
                url: '/pages/authorize/index'
            });
        }
    }
}

exports.routerFilter = routerFilter;

// 在需要使用的页面进行引用并调用

import filter from '@/utils/pageFilter';
Page(filter.routerFilter({
    onLoad(e){
        // 业务代码
    }
}));
```

### 方案二：wepy框架里面的api拦截
- 参看[wepy官方文档有关拦截器的介绍](https://wepyjs.gitee.io/wepy-docs/1.x/#/?id=interceptor-%e6%8b%a6%e6%88%aa%e5%99%a8)，进行如下代码设置

``` bash
// app.wpy
  constructor () {
    // 拦截页面的跳转
    // 对以下几个API进行拦截处理
    this.intercept('navigateTo', this.filterRouter());
    this.intercept('redirectTo', this.filterRouter());
    this.intercept('reLaunch', this.filterRouter());
    this.intercept('navigateBack', this.filterRouter());
  }
  // 路由拦截
  filterRouter(){
    return {
      config(p){
        const isCHecked = wx.getStorageSync('isChecked');
        if(!isCHecked){
          p.url ='';
          return p
        }
        return p;
      },
      success(p){},
      fail(p){
        Tips.modal('系统维护中');
      },
      complete(p){}
    }
  }
```
- 针对公众号及二维码等其他方式进入小程序指定页面的场景，需要在具体页面进行拦截。
参照官网中关于[Mixin 混合](https://wepyjs.gitee.io/wepy-docs/1.x/#/?id=mixin-%e6%b7%b7%e5%90%88)，进行设计
``` bash
// /mixins/building.js
import wepy from 'wepy'

export default class buildingMixin extends wepy.mixin {
  data = {
  }
  methods = {
    building () {
      wx.showModal({
        content: "该功能正在建设中",
        showCancel: false
      });  
    },
  }
  interceptPage(){
    this.$parent.watch('isGetEwmCode', (val)=>{
      const isCHecked = wx.getStorageSync('isChecked');
      val && 
        (!isCHecked) && 
          wepy.switchTab({
            url: '/pages/home/home'
          })
    });
  }
  onShow(){
    this.interceptPage();
  }
}


// 具体页面
import BulidingMixin from '@/mixins/building.js'
export default class Report extends wepy.page {
  mixins = [BulidingMixin]
}

```

至此，完成了路由及页面的拦截