---
title: GL画图功能
date: 2018-09-08T09:43:45+08:00
lastmod: 2018-09-08T09:43:45+08:00
cover: "/images/default1.jpg"
draft: false
categories: ["OpenGL"]
tags: ["GL"]
description: 实现记录鼠标（或触摸）画的图，映射到纹理
---

> ## 画图：
>
> 1. **鼠标（或触摸）画出的图形显示在屏幕上**
>
>    1. 获取鼠标（或触摸）的坐标
>
>    2. 记录鼠标（或触摸）在屏幕的比例
>
>        > **世界坐标系：**以(0, 0)坐标为原点，记录的相对位置。
>        >
>        > **物体坐标系(Local Position)：**以父类坐标为参考，记录的相对位置。
>        >
>        > **相机坐标系：**以相机坐标为参考，记录的相对位置。
>        >
>        > **视图坐标系：**坐标系x y范围为(0 ~ 1)，记录比例。
>        >
>        > **屏幕坐标系(Input.mousePosition)：**原点（左下角）：(0, 0) 右上角：(屏幕分辨率宽, 屏幕分辨率高)。
>
> 2. **在屏幕上画图**
>     1. **Line Renderer**
>     2. **OpenGL**（Open Graphics Library）
>
> 3. **屏幕上画的图形显示到图片或者3D物体上（旗帜、布）**
>
>    1. **截屏：**ScreenCapture.CaptureScreenshot()
>    2. **点在图片上画：**根据顶点坐标设置像素位置
>
> 4. **图形映射到物体：**用新建Texture2D存储图片，再替换修改纹理。
>
>    1. **2D：**替换Image的Sprite
>
>       > **transform.GetComponent<Image>().sprite = Sprite.Create(texture2D, new Rect(0, 0, texture2D.width, texture2D.height), new Vector2(0.5f, 0.5f));**
>
>    2. **3D：**替换Render里的Texture
>
>       > **transform.GetComponent<Renderer>().material.mainTexture = texture2D;**

------


```c#
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class Painter : MonoBehaviour
{
    private Vector3 screenPos;

    private Texture2D painter;

    List<Vector3> allPoints;//allPoints存储画出的点

    void Start()
    {
        allPoints = new List<Vector3>();
    }

    void Update()
    {
        if (Input.GetMouseButton(0))
        {
            screenPos = Camera.main.ScreenToViewportPoint(Input.mousePosition);//当前鼠标所在位置

            Debug.Log("当前鼠标坐标" + screenPos);

            allPoints.Add(screenPos);//存储画出的点
        }

        if(Input.GetMouseButtonUp(0))
        {
            if (transform.name == "_3D_DrawBoard")
            {
                DrawTexture();
            }
            else
            {
                DrawTexture2D();
            }

            allPoints.Clear();
        }
    }
    
    static Material lineMaterial;
    static void CreateLineMaterial()//创建一个材质
    {
        if (!lineMaterial)
        {
            // Unity has a built-in shader that is useful for drawing
            // simple colored things.
            Shader shader = Shader.Find("Hidden/Internal-Colored");
            lineMaterial = new Material(shader);
            lineMaterial.hideFlags = HideFlags.HideAndDontSave;
            // Turn on alpha blending
            lineMaterial.SetInt("_SrcBlend", (int)UnityEngine.Rendering.BlendMode.SrcAlpha);
            lineMaterial.SetInt("_DstBlend", (int)UnityEngine.Rendering.BlendMode.OneMinusSrcAlpha);
            // Turn backface culling off
            lineMaterial.SetInt("_Cull", (int)UnityEngine.Rendering.CullMode.Off);
            // Turn off depth writes
            lineMaterial.SetInt("_ZWrite", 0);
        }
    }

    //将在完成所有常规渲染后调用
    public void OnRenderObject()
    {
        CreateLineMaterial();
        // Apply the line material
        lineMaterial.SetPass(0);

        //将所有顶点矩阵的位置 转换到父类坐标，与父类共同变换位置方向
        //GL.MultMatrix(transform.localToWorldMatrix);

        // LoadOrtho()正交投影 坐标x，y范围为 0~1
        //在屏幕上画图 且图形会按比例显示
        GL.LoadOrtho();

        //开始画 TRIANGLES 三角形，QUADS 四边形，LINES 画线...
        GL.Begin(GL.LINE_STRIP);

        //更换画笔颜色
        GL.Color(Color.red);

        // 1. 画一条直线
        // 两点确定一条直线
        //GL.Vertex3(0, 0, 0);
        //GL.Vertex3(1, 1, 0);

        // 2. 多点画图
        // 依次把存储的顶点 成对画出来
        // for循环 i=1 判断起码有两个点才能继续循环画线
        for (int i = 1; i < allPoints.Count; i++)
        {
            Vector3 frontPoint = allPoints[i - 1];
            Vector3 backPoint = allPoints[i];

            GL.Vertex3(frontPoint.x, frontPoint.y, frontPoint.z);
            GL.Vertex3(backPoint.x, backPoint.y, backPoint.z);
        }

        GL.End();
    }

    //画到3D物体上
    public void DrawTexture()
    {
        //Texture2D来保存图片
        painter = new Texture2D(Screen.width, Screen.height);

        // 根据顶点位置设置图片上的像素
        for (int i = 1; i < allPoints.Count; i++)
        {
            Vector3 tmpFront = allPoints[i - 1];
            Vector3 tmpBack = allPoints[i];

            float frontX = painter.width * tmpFront.x;
            float frontY = painter.height * tmpFront.y;
            float backX = painter.width * tmpBack.x;
            float backY = painter.height * tmpBack.y;

            int lerpPointCount = 200;

            //两点之间插值 添加点
            for (int j = 0; j < lerpPointCount; j++)
            {
                int x = (int)Mathf.Lerp(frontX, backX, j / (float)lerpPointCount);
                int y = (int)Mathf.Lerp(frontY, backY, j / (float)lerpPointCount);

                painter.SetPixel(x, y, Color.red);//根据点位置设置像素点
            }
        }

        painter.Apply();//应用修改到纹理上

        //更换物体纹理
        transform.GetComponent<Renderer>().material.mainTexture = painter;
    }

        //画到2D图片上
    public void DrawTexture2D()
    {
        painter = new Texture2D(Screen.width, Screen.height);

        Rect rect = new Rect(0, 0, painter.width, painter.height);//Sprite的位置长宽

        Vector2 pivot = new Vector2(0.5f, 0.5f);//Sprite的中心点

        Sprite drawSprite = Sprite.Create(painter, rect, pivot);//存储画的图形

        // 根据顶点位置设置图片上的像素
        for (int i = 1; i < allPoints.Count; i++)
        {
            Vector3 tmpFront = allPoints[i - 1];
            Vector3 tmpBack = allPoints[i];

            float frontX = painter.width * tmpFront.x;
            float frontY = painter.height * tmpFront.y;
            float backX = painter.width * tmpBack.x;
            float backY = painter.height * tmpBack.y;

            int lerpPointCount = 200;

            //两点之间插值 添加点
            for (int j = 0; j < lerpPointCount; j++)
            {
                int x = (int)Mathf.Lerp(frontX, backX, j / (float)lerpPointCount);
                int y = (int)Mathf.Lerp(frontY, backY, j / (float)lerpPointCount);

                painter.SetPixel(x, y, Color.red);
            }
        }

        painter.Apply();//应用修改到纹理上

        //更换Image图片纹理
        //Image需创建Sprite后才能修改纹理
        transform.GetComponent<Image>().sprite = drawSprite;
    }
}
```

