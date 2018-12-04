---
title: 【Pain To Learn Unity】——案例之像素鸟
date: 2018-08-11T15:08:23+08:00
lastmod: 2018-08-11T15:08:23+08:00
cover: "/images/default1.png"
draft: false
categories: ["Pain"]
tags: ["Notes" , "2D"]
description: 简单案例之像素鸟
---

<button type="button" class="primary"  style="background-color: #7ED321;width: 90px;height: 40px;color: #FFFFFF">下载.Zip</button>

<button type="button" class="primary"  style="background-color: #7ED321;width: 135px;height: 40px;color: #FFFFFF">下载.unitypacke</button>

# 创建UI界面

## 创建开始菜单界面

>1. 创建button的图片，**按住shift——等比缩放**，再加入button组件。

>2. 创建静音Toggle，背景图设为启动音量图片，前景设为斜线（静音），删除无用文字，关闭Toggle的**Is On**，即可实现初始不显示前景，点击后显示前景图片。

## 添加点击事件

- 首先把菜单所有元素（除了背景）放到一个空物体上，控制空物体Enable达到切换场景目的。

- 然后调整一下UI元素锚点（Anchor Presets）的大致位置。（锚点用于自动适配分辨率，自动调整UI位置）

>1. ra