---
title: Unity Tilemap模块
date: 2018-08-13T08:47:21+08:00
lastmod: 2018-08-13T08:47:21+08:00
cover: "/images/default4.png"
draft: false
categories: ["Unity"]
tags: ["2D", "Tilemap"]
description: 
---

- TileMap(瓦片地图)：
    >可以快速构建关卡，也可以通过相关API代码生成。

    

- 相关组件
    1. >Grid:格子相关属性，TileMap是它子，也是一种画布（Canvas）。

    2. >TileMap:接收绘制好瓦片的地方，类似PS图层。

    3. >TileMap Renderer:控制瓦片渲染效果的组件，比如渲染顺序、遮罩、材质等。

    4. >Tile Palette:控制瓦片中间层，这里选择需要的瓦片。

    5. >Tile:瓦片，里面存储一个Sprite的引用和一个颜色值和碰撞类型。

    6. >2D - extras:TileMap的2D官方拓展包，有拓展笔刷和瓦片
       >
       >https://github.com/Unity-Technologies/2d-extras

       

1. 使用TileMap快速构建关卡



2. 使用RuleTile



3.Cinemachine在2D中使用方法