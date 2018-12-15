---
title: 2D攻击范围
date: 2018-09-09T16:15:05+08:00
lastmod: 2018-09-09T16:15:05+08:00
cover: "/images/default5.ppg"
draft: false
categories: ["2D"]
tags: ["AttackRange"]
description: 2D攻击（碰撞）范围检测
---

>**攻击范围判断：**
>
>1. 物理触发器：极其消耗性能，休闲类可以使用物理触发器，大型游戏不可以。
>
>2. 向量点乘(Vector3.Dot)：
>
>   - **A · B = |A| * |B| * cosθ**	**|A|**就是**A向量长度**
>
>     > 1. 假如 A 是单位向量 |A| = 1 得到 B 在 A 向量上的投影。
>     > 2. 假如 A 和 B 都是单位向量得到 cosθ arcos(A, B) 得到一个弧度。
>
>3. 将被攻击者坐标系变换到攻击者坐标系，在进行 x, z点位置判断

>**矩形攻击：**
>
>**transform.forward：**表示**Player**的**正前方**（forward和right均为单位向量）
>
>**transform.right：**表示**Player**的**正右方**
>
>**-transform.right：**表示**Player**的**正左方**
>
>**-transform.forward：**表示**Player**的**正后方**
>
>**A = Enemy.position - Player.position**（玩家与敌人的距离是A）
>
>  >1. **yLength = A · transform.forward：**等于**A**在**forward线**上的**投影**，**yLength**也是**矩形的宽度y**
>  >   1. **<font color=#12125>A · transform.forward = |A| * cosθ, cosθ范围 (θ：π/2 ~ -π/2 = 0~180°) 从正数到负数</font>**（判断上下方向）。
>  >   2. 如果A是正数说明在前方，负数为后方**（得到敌人在前或后的哪个方向，在进行下一步判断）**
>  >   3. 如果**yLength**大于矩形的宽度说明不在范围内（小于矩形的宽度也不一定在范围内）。
>  >2. **xLength = A · transform.right：**等于**A**在**right线**上的**投影**，**xLength**也是**矩形的长度x。**
>  >   1. xLength正负判断左右方向，如果 abs(|A| * cosθ) <= x 则在矩形范围内。
>
>```c#
>using UnityEngine;
>
>public class RectAttack : MonoBehaviour
>{
>    /// <summary>
>    /// 矩形攻击
>    /// 判断是否在矩形范围内
>    /// </summary>
>    /// <param name="attacker">攻击者</param>
>    /// <param name="attacked">被攻击者</param>
>    /// <param name="rectLength">矩形长度</param>
>    /// <param name="rectWidth">矩形宽度</param>
>    public bool RectAttackRange(Transform attacker, Transform attacked, float rectLength, float rectWidth)
>    {
>        //两者距离
>        Vector3 deltaDis = attacked.position - attacker.position;
>
>        //两者距离在攻击者forward的投影forwardDotDis
>        float forwardDotDis = Vector3.Dot(attacker.forward, deltaDis);
>
>        //判断投影长度是否在矩形的宽度范围中
>        if (forwardDotDis > 0 && forwardDotDis <= rectWidth)
>        {
>            //
>            if (Mathf.Abs(Vector3.Dot(attacker.right, deltaDis)) < rectLength)
>            {
>                return true;
>            }
>        }
>
>        return false;
>    }
>}
>```
>
>

------

> **扇形攻击：**
>
> 1. A · transform.forward
> 2. |A| 要小于圆的半径
>
> ```c#
> 
> ```
>
> ------
>
>

> **判断矩形和扇形的范围相交：**
>
> - 判断矩形中心点是否在扇形范围内，有则一定相交；如果中心点不在，则判断其他点。将面问题转换成点