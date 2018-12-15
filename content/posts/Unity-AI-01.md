---
title: AI逻辑
date: 2018-08-05T18:36:56+08:00
lastmod: 2018-08-05T18:36:56+08:00
cover: "/images/default10.png"
draft: false
categories: ["Unity"]
tags: ["AI"]
description: Navigation AI逻辑。
---

# AI

- Navigation

> 给Enemy添加 New Mesh Agent。
>
> - Acceleration：加速度
> - Angular Speed：转向速度

- 常用方法

  1. SetDestination(player_position);

     > 添加寻找对象。

------

## AI 逻辑

```c#
using UnityEngine;
using System.Collections;
 
public class AI : MonoBehaviour {
 
	//定义怪物的四种状态：站立、行走、奔跑、无所事事
	public const int STATE_STAND=0;
	public const int STATE_WALK=1;
	public const int STATE_RUN=2;
	
	//怪物当前状态
	private int NowState;
	//游戏角色
	public GameObject Hero;
	//怪物思考时间
	public const int AI_THINK_TIME=2;
	//触发怪物攻击的临界距离
	public const int AI_ATTACT_DISTANCE=10;
	
	//上一次思考的时间
	private float LastThinkTime;
	
	void Start () 
	{
		
	}
	
	void Update () 
	{
	   //当敌人与怪物间的距离小于攻击范围半径的时候
	   if(Vector3.Distance(transform.position,Hero.transform.position)<AI_ATTACT_DISTANCE)
	   {
	      //敌人开始奔跑
	      this.GetComponent<Animation>().Play("run");
	      //敌人进入奔跑状态
	      NowState=STATE_RUN;
	      //使敌人面向角色
	      transform.LookAt(Hero.transform);
	      //向玩家靠近
	      transform.Translate(Vector3.forward*Time.deltaTime * 5);
	   }else
	   {
	      //当当前时间与上一次思考时间的差值大于怪物的思考时间时怪物开始思考
	      if(Time.time-LastThinkTime>AI_THINK_TIME)
	      {
	         //开始思考
	         LastThinkTime=Time.time;
	         //获取0-3之间的随机数字
	         int Rnd=Random.Range(0,2);	     
	         //根据随机数值为怪物赋予不同的状态行为
	         switch(Rnd)
	         {
	            case 0:
	            //站立状态
	            this.GetComponent<Animation>().Play("idle");
	            NowState=STATE_STAND;
	            break;
	            
	            case 1:
	            //行走状态
	            //使怪物旋转以完成行走动作
	            Quaternion mRotation=Quaternion.Euler(0,Random.Range(1,5)*90,0);
	            transform.rotation=Quaternion.Slerp(transform.rotation,mRotation,Time.deltaTime*1000);
	            //播放动画
	            this.GetComponent<Animation>().Play("walk");
	            //改变位置
	            transform.Translate(Vector3.forward*Time.deltaTime * 3);
	            NowState=STATE_WALK;
	            break;
	           
	            case 2:
	            //奔跑状态
	            this.GetComponent<Animation>().Play("run");
	            transform.Translate(Vector3.forward*Time.deltaTime * 5);
	            NowState=STATE_RUN;
	            break;
	         } 
	      }
	   }
	}
}

```

------

