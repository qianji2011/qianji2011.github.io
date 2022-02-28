---
title: Object.assign移动端兼容性问题
date: 2021-12-24 17:31:16
tags: 微信小程序
categories: 技术
---
### 问题产生的场景

开发微信小程序的时候，存在用户注册的场景。用户注册一共有两个页面：第一页是基本信息，第二页需要填详细信息，从第一个页面跳转到第二个页面的时候，将多个参数整合到一个对象里面，再把这个对象传递到下个页面。但是部分手机会出现参数缺失的情况

### 问题产生的原因

第一个页面，处理参数的方法是这样的
``` bash
const params = Object.assign({
    name: this.data.userName
}, this.data.baseInfo, {
    mobile: this.data.mobile
})
```
某些型号的手机，进入到第二个页面后，在控制台打印传递过来的是个空对象！！！
没错，就是Object.assign()这个方法存在兼容性问题，部分手机型号是不支持这个语法的

### 解决方案

以下提供两种解决方案

#### 一、注意罗列属性并给对应属性赋值
``` bash
const { name, mobile, baseInfo } = this.data
let params = {
    ...baseInfo
}
if (!params.name) {
    params.name = name
}
params.mobile = mobile
```

#### 二、 先判断是否有这个方法，如果没有则添加方法
``` bash
if (typeof Object.assign !=== 'function') {
    (function(){
        Object.assign = function(target) {
            'use strict';
            if(target === undefined || target === null) {
                throw new TypeError('cannot cover undefined or null to Object')
            }
            var output = Object(target)
            for(var i =1; i < arguments.length; i++) {
                var sources = arguments[i]
                for(var key in sources) {
                    if(sources.hasOwnProperty(key)){
                        output[key] = sources[key]
                    }
                }
            }
            return output
        }
    })()
}
```
