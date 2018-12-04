---
title: EasyTouch模块封装——游戏框架-06
date: 2018-09-07T18:18:05+08:00
lastmod: 2018-09-07T18:18:05+08:00
cover: "/images/default1.png"
draft: false
categories: ["GAME-FRAME"]
tags: ["RPG"]
description: 摇杆封装
---

>## **EasyTouch  制作流程：**
>
> 1. 获取鼠标到圆心的半径
> 	>deltaDistance = 鼠标点 -- 圆心 --> 判断距离
>
> 2. 移动范围
>
>      - 在圆内可以任意移动
>
>      >deltaDistance.magnitude< 半径（**deltaDistance.magnitude = Vector2.Distance()**，求两向量间距离）
>
>      - 超过半径只能在圆上移动
>
>      > 
>
> 3. 实现物体跟着旋转
>
>      - 让图片随着摇杆移动旋转
>        - 切线：Z = Tan α --> Q = ArcTan z（Q 弧度）
>        - 弧度：π = 180°  π / 2 = 3.1415926 / 2 ≈ 1.57 = 90°
>        - 角度：180° = π
>
>      - 摇杆原点随着摇杆移动变化
>
>        - 1
>
>
>
> ## 测试简单  EasyTouch
>
>```c#
>using UnityEngine;
>using UnityEngine.EventSystems;
>
>/// <summary>
>/// 1. 使用IDragHandler必须与其接口访问修饰相同
>/// 2. IBeginDragHandler, IDragHandler, IEndDragHandler 开始拖拽、正在拖拽、结束拖拽
>/// </summary>
>public class JoyStick : MonoBehaviour, IDragHandler, IEndDragHandler
>{
>    Vector2 orginPos;//摇杆中心点起始位置
>
>    public float radius = 100;//摇杆移动范围半径
>
>    public Transform imgTransform;
>
>    void Start()
>    {
>        orginPos = transform.position;
>    }
>
>    // 拖动时：摇杆移动范围为圆形
>    public void OnDrag(PointerEventData CirculareventData)
>    {
>        //相对位置 得到点击的位置点与起始点的向量
>        Vector2 deltaPos = CirculareventData.position - orginPos;
>
>        //点距离圆心长度 小于 移动半径
>        if (deltaPos.magnitude < radius)
>        {
>            //摇杆位置 = 当前点位置
>            transform.position = CirculareventData.position;
>        }
>        else
>        {
>            //摇杆（世界）位置 = 初始化的世界位置 + 相对（单位）向量
>            transform.position = orginPos + deltaPos.normalized * radius;
>
>            //求摇杆移动的角度    反正切(ArcTan(y / x)) * 弧度转角度
>            float angle = Mathf.Atan2(deltaPos.y, deltaPos.x) * Mathf.Rad2Deg;
>
>            //不能直接修改的变量，用临时变量接受赋值回去
>            Vector3 tmpAgnle = imgTransform.localEulerAngles;
>
>            //需要移动摇杆去改变的轴为z
>            tmpAgnle.z = angle;
>
>            imgTransform.localEulerAngles = tmpAgnle;
>        }
>    }
>
>    // 结束拖动时 还原摇杆位置
>    public void OnEndDrag(PointerEventData eventData)
>    {
>        transform.position = orginPos;
>    }
>}
>```
>

------

## EasyTouch  嵌入框架

>```c#
>using UnityEngine;
>using UnityEngine.EventSystems;
>
>public class JoyStick1Ctr : UIBase {
>
>    JoyStick1Logic joyStick1Logic;
>
>    void Start () {
>        GameObject player = GameObject.FindGameObjectWithTag("Player");
>        GameObject img = GetWidget("TestJoyStickImg_N");
>        GameObject joyStick = GetWidget("JoyStick_N");
>        
>        //joyStick1Logic = new JoyStick1Logic();
>        //joyStick1Logic.Ower = player.transform;
>        //joyStick1Logic.BgTransform = img.transform;
>        //joyStick1Logic.ImgTransform = joyStick.transform;
>        //简洁代码 写构造函数
>
>        joyStick1Logic = new JoyStick1Logic(player.transform, img.transform, joyStick.transform);
>
>        AddDrag("TestJoyStickImg_N", joyStick1Logic.OnDrag);
>    }
>
>    void Update()
>    {
>        if (joyStick1Logic != null)
>        {
>            joyStick1Logic.Update();
>        }
>    }
>}
>
>public class JoyStick1Logic
>{
>    //构造函数
>    public JoyStick1Logic(Transform ImgTransform, Transform BgTransform, Transform Ower)
>    {
>        imgTransform = ImgTransform;
>        bgTransform = BgTransform;
>        ower = Ower;
>    }
>
>    public float radius = 90;//摇杆移动范围半径
>    public float speed = 1;
>
>    private Transform imgTransform;
>
>    public Transform ImgTransform { set { imgTransform = value; } }
>
>    private Transform bgTransform;
>
>    public Transform BgTransform { set { bgTransform = value; } }
>
>    private Transform ower;//脚本挂载位置
>
>    public Transform Ower { set { ower = value; } }
>
>    public void OnDrag(BaseEventData baseEventData)
>    {
>        PointerEventData eventData = (PointerEventData)baseEventData;
>
>        //相对位置 得到点击的位置点与起始点的向量
>        Vector2 deltaPos = eventData.position - (Vector2)imgTransform.position;
>
>        //点距离圆心长度 小于 移动半径
>        if (deltaPos.x < radius)
>        {
>            //摇杆位置 = 当前点位置
>            ower.position = eventData.position;
>        }
>        else
>        {
>            //摇杆（世界）位置 = 初始化的世界位置 + 相对（单位）向量
>            ower.position = (Vector2)imgTransform.position + deltaPos.normalized * radius;
>
>            //求摇杆移动的角度    反正切(ArcTan(y / x)) * 弧度转角度
>            float angle = Mathf.Atan2(deltaPos.y, deltaPos.x) * Mathf.Rad2Deg;
>
>            //不能直接修改的变量，用临时变量接受赋值回去
>            Vector3 tmpAgnle = ower.localEulerAngles;
>
>            //需要移动摇杆去改变的轴为z
>            tmpAgnle.z = angle;
>
>            ower.localEulerAngles = tmpAgnle;
>        }
>    }
>
>    public void Update()
>    {
>        bgTransform.position = Vector2.Lerp(bgTransform.position,
>                                            ower.position,
>                                            Time.deltaTime * speed);
>    }
>}
>```
>
>