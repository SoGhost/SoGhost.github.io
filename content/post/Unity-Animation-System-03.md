---
title: Unity 动画系统（3）
date: 2018-08-09T12:47:45+08:00
lastmod: 2018-08-09T12:47:45+08:00
cover: "/images/default1.png"
draft: false
categories: ["Unity-Animator"]
tags: ["Animator"]
description: 动画的编辑_Animation View介绍、使用
---

# Animation View

> Animation View可以修改的类型：Float, Color, Vector2, Vector3, Vector4, Quaternion, Boolean.
>
> - Add Key:添加特征点
> - Add Event:添加事件系统

- Add Property：增加动画变换属性

  > 可以给给所有物体添加动画。（制作摄像机抖动）

- Root Motion

  >- [x] 选中后该物体会相对于（目标）物体坐标移动，否则相对于世界坐标移动。

- Animation Event
  >方便实现**角色着陆音效**与**运动镜头抖动**。
  - 可执行函数
    >1. 必须为**Public**
    >
    >2. 函数返回为**Void**
    >
    >3. 只有**一个参数**
    >
    >4. 输入参数类型包括：**int, float, string, Object**
    >
    >   ```c#
    >   public class testAnimationEvent : MonoBehaviour{
      
          public void testPrintLog(float msg){
          Debug.Log("Animation Event: " + msg.ToString());
          }
    }
    >   ```
    >回到Animation View，编辑Event，传参。
  >如果在某个动画中实现Event，则需在模型的Animation View中点Event去添加事件。
