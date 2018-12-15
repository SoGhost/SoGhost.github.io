---
title: MonoBehavior 常用方法
date: 2018-07-30T15:04:16+08:00
lastmod: 2018-07-30T15:04:16+08:00
cover: "/img/default8.png"
draft: false
categories: ["Unity"]
tags: ["Unity"]
description: 事件行为 常用方法
---

# 继承函数

- 继承自Object
  - Destroy：物体销毁

   - Instantiate：物体生成

   - CompareTag：物体辨认

- 继承自Component

  - SendMessage：消息推送

# 自身函数

1.Invoke：延迟执行程序

- Invoke(string methodName, float time);
- InvokeRepeating(string methodName, float time, float repeatRate);


> 重复调用函数，重复间隔。

- IsInvoking(string methodName);

>检测是否在继续调用。

- CanceInvoke();
  - CanceInvoke(string methodName);

> 清楚所有Invoke或针对某一函数。

>```c#
>
>```

------

2.Coroutine：协同执行程序