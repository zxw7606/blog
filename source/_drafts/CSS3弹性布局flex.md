---
thumbnail: /images/.jpeg
title: CSS3弹性布局flex
widgets:
  - type: recent_posts
    position: right
  - type: toc
    position: right
toc: true
date: 2020-08-03 20:48:00
tags: [CSS, CSS3]
categories:
---

## 盒子模型
![弹性布局](CSS3弹性布局flex/flex-direction-terms.svg)
[参考链接](https://www.w3.org/TR/2018/CR-css-flexbox-1-20181119/#box-model)

> A flex container is the box generated by an element with a computed display of flex or inline-flex. In-flow children of a flex container are called flex items and are laid out using the flex layout model.

<!-- more -->

可见盒子模型有一个方向从左至右的主轴(main axis)和方向从上到下cross axis(横轴)百度教程说成交叉轴.

举例如下
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>弹性布局</title>
    <style>
        .box {
            text-align: center;
            width: 300px;
            height: 300px;
            background-color: red;
            display: flex; /* 设置弹性布局 */
            /* 以下针对所有的子元素 */
            flex-direction: row; /* 设置元素生成的方向 */
            flex-wrap: wrap; /* 宽度不够自动换行 例如容器宽250, 里面的元素宽度总和300 */
            justify-content: center; /* 水平对齐方式,其实就是元素的布局,间隙等等,围绕main axis */
            align-content: center; /* 垂直对齐方式, 根据cross axis 布局(前提是多行的情况下) */
            align-items: center; /* 垂直对齐方式(上下) 根据cross axis 布局 */
            margin: 0 auto;
        }

        div[class^="child-box"] {
            width: 100px;
            height: 100px;
        }
    </style>
</head>

<body>
<div class="box">
    <div class="child-box1">
        1
    </div>
    <div class="child-box2">
        2
    </div>
    <div class="child-box3">
        3
    </div>
</div>
</body>
</html>
```