---
title: FPS_视角和移动
date: 2018-08-05T14:48:37+08:00
lastmod: 2018-08-05T14:48:37+08:00
cover: "/images/default3.png"
draft: false
categories: ["Unity"]
tags: ["FPS"]
description: 鼠标和键盘控制移动。
---

## 主要涉及知识

- 1、Input.GetAxis(“Mouse X”)：获取鼠标延左右移动的速度
- 2、Input.GetAxis(“Mouse Y”)：获取鼠标延上下移动的速度
- 3、Input.GetKeyDown(“escape”)：按下esc键
- 4、transform.Rotate()：使物体旋转，如果最后一个参数relativeTo默认以局部坐标旋转，如果设置为-Space.World（世界坐标系），则旋转应用于世界x，y，z轴。
- 5、transform.localEulerAngles：物体的欧拉角（相对于父级的坐标），此例子我们直接改变物体的欧拉角以达到旋转的效果。和用transform.Rotate()改变有所区别
- 6、Mathf.Clamp()：
- 7、Cursor.lockState = CursorLockMode.Locked;将光标锁定到游戏窗口的中心。 
  Cursor.lockState = CursorLockMode.Confined;将光标限制在游戏窗口。
- 8、Cursor.visible = false;隐藏光标
- 9、CharacterController：CharacterController类。CharacterController允许你简单地做碰撞约束的移动，而不必处理刚体。
- 10、CharacterController.Move()：通过运动移动控制器，并且受碰撞约束，会延着碰撞体表面滑动，它不受重力影响。（相对于世界坐标系来移动物品）
- 11、transform.TransformDirection()：转换为世界坐标系
- 12、Vector3.ClampMagnitude()：限制一个向量的最大长度。

## 镜头跟随鼠标旋转

```c#
using UnityEngine;
using System.Collections;

public class MouseLook : MonoBehaviour {
    //定义枚举数据结构，将名称和设置关联起来
    public enum RotationAxes
    {
        MouseXAndY = 0,
        MouseX = 1,
        MouseY = 2
    }
    public RotationAxes axes = RotationAxes.MouseXAndY;
    public float sensitivityHor = 9.0f;//水平旋转的速度
    public float sensitivityVert = 9.0f;//垂直旋转的速度
    public float minimumVert = -45.0f;//垂直旋转的最小角度
    public float maximumVert = 45.0f;//垂直旋转的最小角度

    private float _rotationX = 0;//为垂直角度声明一个私有变量

    void Start()
    {
        //将光标锁定到游戏窗口的中心。
        Cursor.lockState = CursorLockMode.Locked;
        Cursor.visible = false;
    }

    void Update ()
    {
        if (axes == RotationAxes.MouseX)
        {
            //水平旋转代码
            transform.Rotate(0,Input.GetAxis("Mouse X") * sensitivityHor, 0);
        }
        else if (axes == RotationAxes.MouseY)
        {
            //垂直旋转代码
            _rotationX -= Input.GetAxis("Mouse Y") * sensitivityVert;
            _rotationX = Mathf.Clamp(_rotationX,minimumVert,maximumVert);

            float rotationY = transform.localEulerAngles.y;//保持y轴与原来一样
            transform.localEulerAngles = new Vector3(_rotationX, rotationY, 0);
        }
        else
        {
            //水平且垂直旋转
            _rotationX -= Input.GetAxis("Mouse Y") * sensitivityVert;
            _rotationX = Mathf.Clamp(_rotationX, minimumVert, maximumVert);//限制角度大小

            float delta = Input.GetAxis("Mouse X") * sensitivityHor;//设置水平旋转的变化量
            float rotationY = transform.localEulerAngles.y+delta;//原来的角度加上变化量
            transform.localEulerAngles = new Vector3(_rotationX,rotationY,0);//相对于全局坐标空间的角度
        }

        //当点击esc后
        if (Input.GetKeyDown("escape"))
        {
            Cursor.visible = true;
            Cursor.lockState = CursorLockMode.None;
            //Cursor.lockState = CursorLockMode.Confined;//将光标限制在游戏窗口。
        }
        if (Input.GetButtonDown("Fire1"))
        {
            Cursor.visible = false;
            Cursor.lockState = CursorLockMode.Locked;
        }
    }
}
```

------

## 键盘控制角色移动

```C#
using UnityEngine;
using System.Collections;

[RequireComponent(typeof(CharacterController))]//RequireComponent表示此物体需要有 CharacterController才能运行此脚本,否则会报错
[AddComponentMenu("Control Script/FPS Input")]//AddComponentMenu表示脚本将在unity编辑器中添加组件菜单。以后想添加FPSInput脚本，可以在AddComponent中添加
public class FPSInput : MonoBehaviour {

    public float speed = 1f;
    public float gravity = -9.8f;

    private CharacterController _charController;

    void Start()
    {
        _charController = GetComponent<CharacterController>();
    }

    void Update ()
    {
        float deltaX = Input.GetAxis("Horizontal")*speed;
        float deltaZ = Input.GetAxis("Vertical") * speed;
        //transform.Translate(deltaX*Time.deltaTime, 0, deltaZ * Time.deltaTime);//此方法直接相对于本体移动

        Vector3 movement = new Vector3(deltaX,0, deltaZ);//此处是相对于本体的坐标
        movement = Vector3.ClampMagnitude(movement, speed);//限制向量的最大长度，并返回此变化后的向量
        movement.y = gravity;

        movement *= Time.deltaTime;
        movement = transform.TransformDirection(movement);//转换为世界坐标系

        _charController.Move(movement);//move()是现对与世界坐标系移动物体的。
    }
}
```

