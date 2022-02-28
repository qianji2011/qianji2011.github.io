---
title: Date移动端兼容性问题
date: 2022-01-05 16:51:02
tags: 微信小程序
categories: 技术
---
开发过程中经常需要对日期数据进行操作，例如：格式化，计算时间差等。下面就是应用时间对象进行日期数据格式化的时候，移动端发现的兼容性问题

### 原时间处理代码

对于 YYYY-MM-DD 的日期格式，new Date() 方法在某些系统中会产生兼容性问题

``` bash
// 需要处理的日期数据
const date = '2021-01-09 12:09:08'
// 使用Date对象处理数据
const timer = new Date(date) // 在某些型号的手机上面，这个表达式的计算结果得到的是Invalid Date
// 对时间数据进行处理
const year = timer.getFullYear() // 此处省略
```
### 解决办法

使用replace方法，将日期数据内的'-'替换成'/'

``` bash
// 需要处理的日期数据
const date = '2021-01-09 12:09:08'
// 将 - 替换成 /
const dateNew = date.replace(/-/g,'/')
// 使用Date对象处理数据
const timer = new Date(dateNew)
// 对时间数据进行处理
const year = timer.getFullYear() // 此处省略
```

这样就可以解决new Date()方法的兼容性问题了