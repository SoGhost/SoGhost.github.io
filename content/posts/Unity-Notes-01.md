---
title: Unity 注意事项
date: 2018-07-21T10:38:10+08:00
lastmod: 2018-07-21T10:38:10+08:00
cover: "/img/default2.jpg"
draft: false
categories: ["Unity"]
tags: ["unity"]
description:
---

# 疑难杂症——👇

## 一亿个注意:

### 1.Local和Global

><font color=#191970>Local是指的自身的坐标，Global指的是世界坐标。
>
>假如一个物体没有父物体（即这个物体不是某个物体的子物体），或者父物体的坐标为（0，0，0）。这时候Local和Global的坐标是一样的。
>
>假如有父物体，Local坐标是相对与父物体的坐标，而Global是指在空间中实际的坐标。</font>

### 2.Awake()和Start()

> <font color=#008080>Awake()在游戏开始时自动调用，无论是否激活脚本组件都能被调用，一般用来创建变量。
>
> Start()在Awake之后Update之前，一般用来初始化变量。</font>

### 3.Update()和FixUpdate()

> Update()每一帧调用一次，一般用于非物理运动，如逻辑运算 。
>
> Fixupdate()每隔固定时间调用一次，通过Edit->Project Settings->Time 进行设置，默认为20毫秒 ，一般用于物理运动。

### 4.transform01translate0

>transform是Gameobject的位置属性，translate是一个动作属性。
>
>获取某个object的位置信息时就是transform，若是对其做动作，那就translate。

### 5.