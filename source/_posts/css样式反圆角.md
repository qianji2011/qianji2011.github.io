---
title: css样式反圆角
date: 2022-04-23 23:05:07
tags: css
---
一般css样式中，使用比较多的是圆角,也就是border-radius，这个是圆心朝向矩形内部的。今天需要实现的是反圆角，也就是圆心朝外的圆角。

### 1.要实现的效果
要实现的效果简图如下
![反圆角](https://i.bmp.ovh/imgs/2022/04/23/c4227983f0aaadab.png)

### 2.分析
很明显，这是两个带圆角的矩形组合的，然后在两个矩形重合的地方使用反圆角。

### 3.实现步骤
- 3.1 html中先构建两个div
``` bash
<div id="top-div" class="base-div"></div>
<div id="bottom-div" class="base-div"></div>
```
- 3.2 分别给两个div确定宽高，并做圆角
``` bash
// css样式
.base-div {
    width: 200px;
    border-radius: 24px;
}
#top-div {
    height: 100px;
}
#bottom-div {
    height: 300px;
}
```
- 3.3 背景色处理
``` bash
        body {
            background-color: #ff0;
        }
        .base-div {
            width: 200px;
            border-radius: 24px;
        }
        #top-div {
            height: 100px;
            background: radial-gradient(circle at bottom right, transparent 20px, #fff 0) bottom right,
                    radial-gradient(circle at bottom left, transparent 20px, #fff 0) bottom left;
            background-size: 50% 100%;
            background-repeat: no-repeat; 
        }
        #bottom-div {
            height: 300px;
            background: radial-gradient(circle at top left, transparent 20px, #fff 0) top left,
                    radial-gradient(circle at top right, transparent 20px, #fff 0) top right;
            background-size: 50% 100%;
            background-repeat: no-repeat;
        }
```

这里需要注意的是，两个div都没有设置边框

### 实际效果
![](https://i.bmp.ovh/imgs/2022/04/23/ef3c5b09e06380fd.png)

至此，完成反向圆角的设置