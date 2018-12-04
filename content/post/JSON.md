---
title: JSON格式
date: 2018-08-06T07:42:20+08:00
lastmod: 2018-08-06T07:42:20+08:00
cover: "/images/default6.png"
draft: false
categories: []
tags: ["数据传输"]
description: Json数据结构及工具LitJson
---

## JSON简介

>JSON：是一种轻量级的数据交换格式。基于JavaScript的一个子集。JSON采用完全独立于语言的文本格式。Unity主要使用JSON进行服务器端和客户端数据传输。

## JSON结构

> 通过对象、数组两种结构，就可以组成复杂的数据结构。

- JSON对象

>对象：表示为 ‘ {} ’ 括起来的内容，数据结果为键值对的结构。
>
>{ "name" : "Test Name" }
>
>属性可以为 数字、字符串、数组、对象。

- JSON数组

> 数组：中括号 ' [ ] ' 括起来的内容，数据结构为 [ "C#", "Unity", ...] ，取值方式和所有语言一样，使用索引获取，字段值的类型可以是 数字、字符串、数组、对象。

------

- JSON示例

```json
//Json对象
{ "name":"Ted", "Lv":1, "Job":"法师", "Exp":1.1 }
//复杂Json对象
{ "name":"Jack", "info":{ "Lv":2, "Job":"战士", "Exp":2.2 }}
//Json数组
[
    { "name":"Ted", "Lv":1, "Job":"法师", "Exp":1.1 },
    { "name":"Jack", "Lv":2, "Job":"战士", "Exp":2.2 }
]
```

------

## LitJson工具