---
title: 微信小程序picker双向绑定问题
date: 2022-01-19 15:55:09
tags: 微信小程序
categories: 技术
---

微信小程序开发过程中，通常会使用下拉框组件picker，进行时间，三级省市区等数据选择。
其中，在[微信开放文档](https://developers.weixin.qq.com/miniprogram/dev/framework/view/two-way-bindings.html)上，有这么一段描述

![简易双向绑定](https://s3.bmp.ovh/imgs/2022/01/ed07b781bb8f31d9.png)
哦豁，原来有双向绑定，那就用起来。然而，实际应用的时候，部分手机型号并不支持这样的写法

### 问题出现的场景

废话不多说，直接上代码

- wxml代码
``` bash
<van-field
  required
  is-link
  label="水果">
    <view class="form-left" slot="left-icon"><image mode="widthFix" src="../../images/fruite.png" style="width: 26rpx; height: 26rpx;" /></view>
    <picker
    slot="input"
    mode="selector"
    name="fruite"
    data-name="fruite"
    model:value="{{fruiteIndex}}"
    range="{{fruites}}"
    range-key="name">
      <block wx:if="{{fruiteIndex !== null}}">{{fruites[fruiteIndex].name}}</block>
      <block wx:else><text class="selector-placeholder-text">请选择您最喜欢的水果</text></block>
    </picker>
</van-field>
```

- js 代码

``` bash
data: {
    fruites:[
        {
            name: '苹果',
            keyword: 'apple'
        },
        {
            name: '香蕉',
            keyword: 'banana'
        }
    ],
    fruiteIndex: null
}
```
在开发者工具和体验版调试的时候没有问题。但是上了正式版发布之后，用户群体增加之后，部分机型，如荣耀6X就会出现这样的问题：弹框出来，选择某个选项并点确定后，fruiteIndex并没有被赋值


### 问题定位

一开始，怀疑是vant组件的原因，因为picker是被van-field包裹的，是不是这个组件的兼容性问题呢？
为此，去[vant/weapp](https://vant-contrib.gitee.io/vant-weapp/#/field)查看这个组件的调用方法，对比了自己的代码，也没发现有什么不对的地方，同时也在网上搜索，也没人反馈有兼容性问题。
排除掉外层的组件，剩下的就是原生的picker。
而这个原生picker的属性中，不常用的就是model:value这样的写法。而且官方文档中的例子是input组件，且只能对单一字段进行绑定。

### 解决方案

wxml中，去掉model:value熟悉，使用value属性，并增加监听onChange事件；js代码中，onChange事件处理方法内，使用setData同时更新视图与data

- wxml代码
``` bash
<van-field
  required
  is-link
  label="水果">
    <view class="form-left" slot="left-icon"><image mode="widthFix" src="../../images/fruite.png" style="width: 26rpx; height: 26rpx;" /></view>
    <picker
    slot="input"
    mode="selector"
    name="fruite"
    data-name="fruite"
    value="{{fruiteIndex}}"
    bindchange="onChangeFriute"
    range="{{fruites}}"
    range-key="name">
      <block wx:if="{{fruiteIndex !== null}}">{{fruites[fruiteIndex].name}}</block>
      <block wx:else><text class="selector-placeholder-text">请选择您最喜欢的水果</text></block>
    </picker>
</van-field>
```

- js代码
``` bash
data: {
    fruites:[
        {
            name: '苹果',
            keyword: 'apple'
        },
        {
            name: '香蕉',
            keyword: 'banana'
        }
    ],
    fruiteIndex: null
},
onChangeFriute(e) {
    this.setData({
        fruiteIndex: e.detail.value
    })
}
```

这样就完美解决了这个问题。
小程序发布后，经过长时间检测，没有用户反馈出现这个问题了。
所以，有些功能还是老老实实自己码代码实现。特别是官方例子里面没有使用的，不要盲目自信的添加到项目中，谁知道会不会出什么幺蛾子呢