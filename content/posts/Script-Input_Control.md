---
title: 脚本的输入与控制
date: 2018-07-30T16:36:45+08:00
lastmod: 2018-07-30T16:36:45+08:00
cover: "/images/default4.png"
draft: false
categories: ["Unity"]
tags: ["unity"]
description: 
---

# 鼠标输入

## 1.OnMouse方法

>OnMouseEnter()：当鼠标进入GUIElement或Collider时调用。（只调用一次）
>
>OnMouseExit()：鼠标离开GUIElement或Collider时调用。
>
>OnMouseOver()：鼠标悬停在GUIElement或Collider上时调用每一帧。
>
>OnMouseDown()：鼠标在GUIElement或Collider上点击时调用。
>
>OnMouseIp()：释放鼠标按钮时调用。
>
>OnMouseDrag()：鼠标拖拽GUIElement或Collider时调用。
>
>OnMouseUpAsButton()：只有当鼠标在同一个GUIElement或Collider按下，释放时调用。

```c#
public class ObjectController : MonoBehaviour {

    private void OnMouseEnter()//当鼠标进入GUIElement或Collider时调用。（只调用一次）
    {
        this.transform.localScale = new Vector3(1.3f, 1.3f, 1.3f);//物体放大1.3倍
    }

    private void OnMouseExit()//鼠标离开
    {
        this.transform.localScale = new Vector3(1f, 1f, 1f);//大小还原
        this.transform.rotation = new Quaternion(0f,0f,0f,0f);//位置还原
    }

    private void OnMouseOver()//鼠标悬停在GUIElement或Collider上时调用每一帧。
    {
        this.transform.Rotate(Vector3.up, 45 * Time.deltaTime, Space.Self);//随时间旋转
    }
}
```

## 2.Ray

# 键盘输入

# 触屏输入