---
title: Unity 动画系统（2）
date: 2018-08-08T20:19:02+08:00
lastmod: 2018-08-08T20:19:02+08:00
cover: "/images/default7.png"
draft: false
categories: ["Unity-Animator"]
tags: ["Animator"]
description: 模型与动画的创建和导入及注意事项
---

# 模型创建

1. Modelling 建模
  >Unity以米为单位（一单位长度）。

2. Rigging 绑定骨骼
  >最少有15个关节，遵循拓扑结构

3. Skinning 蒙皮

# 模型导入

- 通用文件导入（.fbx，.obj，.3DS，.dfx，.dea）

  > .fbx：存储骨骼和动画
  >
  > 通用文件比较节省资源

- 原始模型导入（.MAX，.MB，.Blend，.MA）

  >不过需要预装对应软件
  >
  >方便，但浪费较大空间

# 模型配置

| Model                           | rig                      | Animations |
| ------------------------------- | ------------------------ | ---------- |
| Scale Factor(比例系数)          | Animation Type(骨骼状态) |            |
| Mesh Compression(网格压缩)      |                          |            |
| Generate Lightmap(生成光照贴图) |                          |            |

# 创建动画