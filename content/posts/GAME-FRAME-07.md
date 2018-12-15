---
title: 小地图封装——游戏框架-07
date: 2018-09-08T07:59:20+08:00
lastmod: 2018-09-08T07:59:20+08:00
cover: "/images/default1.png"
draft: false
categories: ["GAME-FRAME"]
tags: ["RPG"]
description: 小地图(mini-map)封装，与项目实战
---

> ## 小地图(mini-map)---DEMO：
>
> - 两个相机（消耗性能）
>
> - (3D-->2D)等比例映射：	控制的物体位置实时映射到地图上的图片中
>
> 	1. 求出物体与地图的比例：Player位置 - 地形位置 = Player相对于地形的位置 = tmpPos
> 	2. tmpPos.x / 地形宽度 = x比例
> 	3. tmpPos.z / 地形长度 = y比例
> 	4. (地图背景图片 * x比例, 地图背景图片 * y比例) = 地图里Player图片的位置
>
> ```c#
> using UnityEngine;
> 
> /// <summary>
> /// 1. 实现映射位置到小地图上
> /// 2. MiniMap挂载到Player地图的图片上
> /// </summary>
> public class MiniMap : MonoBehaviour {
> 
>     public Transform player;
> 
>     public  RectTransform parenrRect;//Player父类位置（mini-map背景图）
> 
>     public Terrain terrain;
> 
>     float xRatio;
>     float yRatio;
>     //float xPos;
>     //float yPos;
> 
>     Vector2 vector2 = -Vector2.one;// Vector2.one=(1, 1)
> 
>     Vector3 deltaPos;
> 
>     void Start()
>     {
>         //从地图中Player图片上获取父物体（地图背景图片）的RectTransform
>         parenrRect = transform.parent.GetComponent<RectTransform>();
>     }
> 
>     void Update () {
>         //Player与Terrain相对位置
>         deltaPos = player.position - terrain.transform.position;
> 
>         //Player与Terrain的x y 比例
>         //terrain.terrainData.size.x z 地形的长宽
>         xRatio = deltaPos.x / terrain.terrainData.size.x;
>         yRatio = deltaPos.z / terrain.terrainData.size.z;
> 
>         //父类 背景图片的宽 parenrRect.sizeDelta.x
>         //父类 背景图片的长 parenrRect.sizeDelta.y
> 
>         //mini-map 图片等比缩放后mini-map x,y坐标位置
>         // eg 1. 
>         // xPos = parenrRect.sizeDelta.x * xRatio;
>         // yPos = parenrRect.sizeDelta.y * yRatio;
> 
>         // eg 2. 进一步精简代码
>         vector2.x = parenrRect.sizeDelta.x * xRatio;
>         vector2.y = parenrRect.sizeDelta.y * yRatio;
> 
>         //mini-map Player相对于父类的位置
>         transform.localPosition = vector2;
>     }
> }
> ```
>
> - 计算放在Update中（如果为60帧，≈0.16s就要计算一次数据），应该用计时器适当缩短计算间隔。
>
>   ```c#
>       float timeCount = 0;//计时器
>   
>   	// 3. 根据地图大小适当缩减计算间隔
>      	public void CalculatePosition()
>       {
>           timeCount += Time.deltaTime;
>   
>           if (timeCount > 0.2f)
>           {
>               timeCount = 0;
>   
>               deltaPos = player.position - terrain.transform.position;
>   
>               xRatio = deltaPos.x / terrain.terrainData.size.x;
>               yRatio = deltaPos.z / terrain.terrainData.size.z;
>   
>               vector2.x = parenrRect.sizeDelta.x * xRatio;
>               vector2.y = parenrRect.sizeDelta.y * yRatio;
>   
>               transform.localPosition = vector2;
>           }
>       }
>   
>   	void Update(){
>           CalculatePosition();
>   	}
>   ```
>
------

## War3小地图

> [<font color=#6A5ACD>GL绘图参考链接</font>](https://sofuck.top/post/gl-drawing "GL绘图部分参考链接")
>
> 小地图：根据相机可视大小对应变化到小地图上小矩形的大小。
>
> 1. 得到相机可视区域
>    - 找到相机四个角对应的世界位置
>      - 从相机四个角发出射线，射向地面
>
> 2. 将可视区域映射到小地图上(等比例映射)
>    - 将对应的世界位置映射到小地图上
>      - 相对地形的位置 = 世界坐标 - 地形的位置
>      - 映射比例 = 相对地形的位置 / 地形长宽
>      - 相对小地图位置 = 小地图长宽 * 映射比例 
>
> 3. 将位置画出来显示在小地图上
>
>    1. 根据点位置画直线
>    2. 根据比例画直线
>
> ------
>
> ### CameraView
>
> ```c#
> using UnityEngine;
> 
> public class CameraView : MonoBehaviour {
> 
>     public Terrain terrain;
> 
>     Vector3[] rayPoint;//存储所有射线
> 
>     void Start () {
>         rayPoint = new Vector3[4];//总共有四条射线，存入数组
> 
>         //初始化
>         for (int i = 0; i < rayPoint.Length; i++)
>         {
>             rayPoint[i] = Vector3.zero;
>         }
>     }
> 	
> 	void Update () {
>         //从视图坐标系发出射线，范围只有0~1，方便找到相机四个角位置
>         Ray zeroRay = Camera.main.ViewportPointToRay(Vector3.zero);
>         Debug.DrawRay(zeroRay.origin, zeroRay.direction, Color.red);//打印射线
> 
>         Ray rightRay = Camera.main.ViewportPointToRay(Vector3.right);
>         Debug.DrawRay(rightRay.origin, rightRay.direction, Color.blue);
> 
>         Ray upRightRay = Camera.main.ViewportPointToRay(Vector3.one);
>         Debug.DrawRay(upRightRay.origin, upRightRay.direction, Color.green);
> 
>         Ray upRay = Camera.main.ViewportPointToRay(Vector3.up);
>         Debug.DrawRay(upRay.origin, upRay.direction, Color.cyan);
> 
>         RaycastHit(zeroRay, rightRay, upRightRay, upRay);
>     }
> 
>     public Vector2 GetRatio(int index)
>     {
>         Vector2 resul = Vector2.zero;//把映射比例存在Vector2中
> 
>         //防止index越界(范围永远是0~3)
>         index = index % rayPoint.Length;
> 
>         Vector3 localToTerrain = rayPoint[index] - terrain.transform.position;
>         
>         resul.x = localToTerrain.x / terrain.terrainData.size.x;
>         resul.y = localToTerrain.z / terrain.terrainData.size.z;
> 
>         return resul;
>     }
> 
>     public void RaycastHit(Ray zeroRay, Ray rightRay, Ray upRightRay, Ray upRay)
>     {
>         RaycastHit hitOut;
> 
>         float distance = 1000;//射线射向地面最长距离
> 
>         if (Physics.Raycast(zeroRay, out hitOut, distance))
>         {
>             rayPoint[0] = hitOut.point;
>         }
> 
>         if (Physics.Raycast(rightRay, out hitOut, distance))
>         {
>             rayPoint[1] = hitOut.point;
>         }
> 
>         if (Physics.Raycast(upRightRay, out hitOut, distance))
>         {
>             rayPoint[2] = hitOut.point;
>         }
> 
>         if (Physics.Raycast(upRay, out hitOut, distance))
>         {
>             rayPoint[3] = hitOut.point;
>         }
>     }
> }
> ```
>
> ------
>
> ### MiniMapView
>
> ```c#
> using UnityEngine;
> 
> public class MiniMapView : MonoBehaviour {
> 
>     CameraView cameraView;// CameraView脚本
> 
>     RectTransform rectTrans;
> 
>     void Start(){
> 
>         cameraView = Camera.main.GetComponent<CameraView>();//获取Camera上的CameraView脚本
> 
>         rectTrans = transform.GetComponent<RectTransform>();
>     }
> 
>     void Update () {
> 
>     }
> 
>     public Vector2 CaculatePosition(int index)
>     {
>         Vector2 result = Vector2.zero;
> 
>         Vector2 tmpRate = cameraView.GetRatio(index);
> 
>         result.x = rectTrans.sizeDelta.x * tmpRate.x;
>         result.y = rectTrans.sizeDelta.y * tmpRate.y;
> 
>         return result;
>     }
> 
>     static Material lineMaterial;
>     static void CreateLineMaterial()
>     {
>         if (!lineMaterial)
>         {
>             Shader shader = Shader.Find("Hidden/Internal-Colored");
>             lineMaterial = new Material(shader);
>             lineMaterial.hideFlags = HideFlags.HideAndDontSave;
>             // Turn on alpha blending
>             lineMaterial.SetInt("_SrcBlend", (int)UnityEngine.Rendering.BlendMode.SrcAlpha);
>             lineMaterial.SetInt("_DstBlend", (int)UnityEngine.Rendering.BlendMode.OneMinusSrcAlpha);
>             // Turn backface culling off
>             lineMaterial.SetInt("_Cull", (int)UnityEngine.Rendering.CullMode.Off);
>             // Turn off depth writes
>             lineMaterial.SetInt("_ZWrite", 0);
>         }
>     }
> 
>     public void OnRenderObject()
>     {
>         CreateLineMaterial();
> 
>         lineMaterial.SetPass(0);
>         
>         GL.Begin(GL.LINES);
> 
>         GL.MultMatrix(transform.localToWorldMatrix);
> 
>         GL.Color(Color.red);
> 
>         for (int i = 1; i < 5; i++)
>         {
>             Vector2 tmpFront = CaculatePosition(i - 1);
>             Vector2 tmpBack = CaculatePosition(i);
> 
>             GL.Vertex3(tmpFront.x, tmpFront.y, 0);
>             GL.Vertex3(tmpBack.x, tmpBack.y, 0);
>         }
> 
>         GL.End();
>     }
> }
> ```
>
>