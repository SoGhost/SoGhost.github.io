---
title: Unity 动画系统（1）
date: 2018-08-06T11:45:28+08:00
lastmod: 2018-08-06T11:45:28+08:00
cover: "/images/default5.png"
draft: false
categories: ["Unity-Animator"]
tags: ["Animator"]
description: 动画系统_简介、基本操作、常用网站
---

# Mecanim——<font color=#ADD8E6 >核心功能</font>

- 内置动画状态机系统(Animator)

  >| Controller                           | 角色控制器 |
  >| :----------------------------------- | ---------- |
  >| Avatar (骨骼替身)                    | 替身骨骼   |
  >| Apply Root Motion ( **应用根运动** ) |            |
  >| Update Mode ( 刷新模式 )             |            |
  >| Culling Mode (选择模式)              |            |
  >
  >![](https://sofuck.top/images/MecanimAnimatorComponent.png)

- 人物动画的重定向

  > 更换的Avatar即可重复使用动画。
  >
  > 1.在Model 选取界面，选择 RIG-->Animation Type->Humanoid(人形的)->Apply
  >
  > 2.然后可以配置人物骨骼Avatar。
  >
  > ![](https://sofuck.top/images/MecanimHowItFitsTogether.png)

- 动画的分解与混合

  > **Blend Tree：**混合树
  >
  > 通过Blend Tree混合多个动画。
  >
  > 通过添加其他动画同时播放多个动画。

- 关节动画的 IK 逆向定位

  > IK用于实现角色始终看着一个物体、一个关节与物体绑定。
  >
  > 例如走台阶脚始终与台阶平行 ... ...

------

## 常用网站

1. mixamo.com

   > 有插件，建模、动画一气呵成
   >
   > 可以导入未绑定骨骼模型，选定位置，系统会自动绑定骨骼。
   >
   > 网站有全面的免费动画 …

# 建模软件

# 动画制作软件