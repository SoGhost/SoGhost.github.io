---
title: 音效模块封装——游戏框架-04
date: 2018-08-30T07:19:04+08:00
lastmod: 2018-08-30T07:19:04+08:00
cover: "/images/default9.png"
draft: false
categories: ["GAME-FRAME"]
tags: ["RPG"]
description: 音效模块封装
---

# 音效模块

> **Audio Manager**（音效管理）管理**Source Manager**和**Clips Manager**，Source Manager管理多个AudioSource，Clips Manager管理多个AudioClips。
>
> | 抽象AudioSource：                                     | 抽象AudioClips：           |
> | ----------------------------------------------------- | -------------------------- |
> | 1. 能够从所有的AudioSource中拿出一个空闲的AudioSource | 1. 根据名字找出对应的Clips |
> | 2. 释放多余的（空闲的）AudioSource                    | 2. 通过配置文件读取名字    |
> | 3. 播放一个音效                                       | 3. 加载到内存              |
> | 4. 停止播放                                           |                            |
>
> ------
>
> ```c#
> using System.Collections.Generic;
> using UnityEngine;
> using System.IO;//FileInfo打开文件
> 
> public class UseAudioManager : MonoBehaviour
> {
>     void Update()
>     {
>         if (Input.GetKeyDown(KeyCode.Space))
>         {
>             AudioManager.audiooManager.Play("BGM");
>         }
> 
>         if (Input.GetKeyDown(KeyCode.Space))
>         {
>             AudioManager.audiooManager.Stop("BGM");
>         }
>     }
> }
> 
> public class AudioManager : MonoBehaviour
> {
>     //单例模式
>     public static AudioManager audiooManager;
> 
>     SourceManager sourceManager;
> 
>     ClipManager clipManager;
> 
>     void Start()
>     {
>         audiooManager = this;
> 
>         sourceManager = new SourceManager(gameObject);
> 
>         clipManager = new ClipManager();
>     }
> 
>     public void Play(string audioName)
>     {
>         //获取空闲Source
>         AudioSource audioSource = sourceManager.GetFreeAudioSource();
> 
>         //获取播放音频名字
>         SingleClip tmpClip = clipManager.FindClipByName(audioName);
> 
>         //当获取到音频名字再播放，防止报错
>         if (tmpClip != null)
>         {
>             //调用接口 播放Clip
>             tmpClip.Play(audioSource);
>         }
>     }
> 
>     public void Stop(string audioName)
>     {
>         sourceManager.Stop(audioName);
>     }
> 
> }
> 
> public class SourceManager : MonoBehaviour
> {
>     /// <summary>
>     /// 在new这个对象时，就启动了Initial()
>     /// </summary>
>     /// <param name="tmpOwer"></param>
>     public SourceManager(GameObject tmpOwer)
>     {
>         ower = tmpOwer;
> 
>         Initial();
>     }
> 
>     //存放所有AudioSource
>     List<AudioSource> allSources;
> 
>     GameObject ower;
> 
>     //初始化List
>     public void Initial()
>     {
>         allSources = new List<AudioSource>();
> 
>         for (int i = 0; i < 10; i++)
>         {
>             AudioSource tmpSource = ower.AddComponent<AudioSource>();
> 
>             allSources.Add(tmpSource);
>         }
>     }
> 
>     //获取空闲AudioSource
>     public AudioSource GetFreeAudioSource()
>     {
>         //遍历判断是否有空闲
>         for (int i = 0; i < allSources.Count; i++)
>         {
>             //如果不在播放，返回出去，不再执行
>             if (!allSources[i].isPlaying)
>                 return allSources[i];
>         }
> 
>         AudioSource tmpSource = ower.AddComponent<AudioSource>();
>         allSources.Add(tmpSource);
> 
>         return tmpSource;
>     }
> 
>     /// <summary>
>     /// 释放多余的AudioSource
>     /// </summary>
>     public void ReleaseFreeAudioSource()
>     {
>         int tmpCount = 0;
> 
>         List<AudioSource> tmpSources = new List<AudioSource>();
> 
>         for (int i = 0; i < allSources.Count; i++)
>         {
>             if (!allSources[i].isPlaying)
>                 tmpCount++;
> 
>             if (tmpCount > 3)//空闲的AudioSource数量保持3个
>             {
>                 tmpSources.Add(allSources[i]);
>             }
>         }
> 
>         //移除多余AudioSource
>         for (int i = 0; i < tmpSources.Count; i++)
>         {
>             AudioSource tmpSource = allSources[i];
> 
>             allSources.Remove(tmpSource);//只从数组中移除，场景没有移除
> 
>             Destroy(tmpSource);//从场景中移除组件
>         }
> 
>         tmpSources.Clear();//用完后清空List
>         tmpSources = null;
>     }
> 
>     /// <summary>
>     /// 找到正在播放的AudioSource，并且停止播放
>     /// </summary>
>     /// <param name="audioName"></param>
>     public void Stop(string audioName)
>     {
>         for (int i = 0; i < allSources.Count; i++)
>         {
>             //判断当前AudioSource播放的clip名字与输入的名字是否相等
>             if (allSources[i].isPlaying && allSources[i].clip.name.Equals(audioName))
>             {
>                 allSources[i].Stop();
>             }
>         }
> }
> 
> /// <summary>
> /// 1. 一般从配置文件加载Clip名字
> /// 2. 直接拖到组件上占用空间，占用内存等待播放
> /// </summary>
> public class ClipManager
> {
>     //构造函数
>     public ClipManager()
>     {
>         ReadConfig();
> 
>         LoadClips();
>     }
> 
>     //从配置文件加载Clip名字
>     //string[] clipName = { "Click-one", "Click-two", "Click-three" };
>     string[] clipName;
>     SingleClip[] allSingleClip;
> 
>     /// <summary>
>     /// 加载音频到内存
>     /// </summary>
>     /// <param name="clipName"></param>
>     public void LoadClips()
>     {
>         allSingleClip = new SingleClip[clipName.Length];
> 
>         for (int i = 0; i < clipName.Length; i++)
>         {
>             AudioClip tmpClip = Resources.Load<AudioClip>(clipName[i]);
> 
>             SingleClip tmpSingle = new SingleClip(tmpClip);
> 
>             allSingleClip[i] = tmpSingle;
>         }
>     }
> 
>     /// <summary>
>     /// 根据名字 找到 singleClip
>     /// </summary>
>     public SingleClip FindClipByName(string _clipName)
>     {
>         int tmpIndex = -1;// -1做标记
> 
>         for (int i = 0; i < clipName.Length; i++)
>         {
>             // Equals() 判断两个字符串是否相等
>             if (clipName[i].Equals(_clipName))
>             {
>                 tmpIndex = i;//获得匹配的角标
>             }
> 
>             //不等于-1则 表明找到
>             if (tmpIndex != -1)
>             {
>                 return allSingleClip[tmpIndex];//找到返回
>             }
> 
>             return null;//没找到就返回空
>         }
>     }
> 
>     /// <summary>
>     /// 读取音频的txt配置文件
>     /// </summary>
>     public void ReadConfig()
>     {
>         //获得文件路径
>         var fileAddress = System.IO.Path.Combine(
>             Application.streamingAssetsPath, "AudioConfig.txt");
> 
>         FileInfo fInfo = new FileInfo(fileAddress);//打开文件
> 
>         //如果存在
>         if (fInfo.Exists)
>         {
>             StringReader r = new StringReader(fileAddress);//读取文件
>             //StringReader默认是UTF8，无需再转格式，部分中文字符需要转换
>             //******************** 转成String *******************
>             // byte[] date = new byte[1024];
>             // date = Encoding.UTF8.GetBytes(r.ReadToEnd());
>             // s = Encoding.UTF8.GetString(date, 0, date.Length);
>             //***************************************************
>             string tmpLine = r.ReadToEnd();//只读取一行
> 
>             //解析第一行
>             int lineCount = 0;//行数
> 
>             //试着解析字符串，如果成功返回到lineCount，将字符串变成整数
>             if (int.TryParse(tmpLine, out lineCount))
>             {
>                 Debug.Log("共有" + tmpLine + "行");// 共有3行
> 
>                 clipName = new string[lineCount];
> 
>                 //有几行打印几行
>                 for (int i = 0; i < lineCount; i++)
>                 {
>                     tmpLine = r.ReadToEnd();
> 
>                     //分割字符串，变成数组
>                     string[] splits = tmpLine.Split(" ".ToCharArray());
> 
>                     //Split将字符串分成多个部分，数组从0开始为第一部分，依次递增
>                     Debug.Log(splits[0]);// 0 空格前
>                     Debug.Log(splits[1]);// 1 空格后
> 
>                     clipName[i] = splits[1];
>                 }
> 
>                 r.Close();//用完后关闭
>             }
>             tmpLine = r.ReadToEnd();//读取完
>         }
>     }
> }
> 
> /// <summary>
> /// 1. 专用存储Clip
> /// 2. 需要后续的操作，需要用类存储Clip
> /// </summary>
> public class SingleClip
> {
>     AudioClip audioClip;
> 
>     //构造函数 运行就执行下列代码
>     public SingleClip(AudioClip tmpClip)
>     {
>         audioClip = tmpClip;//替换传入的tmpClip
>     }
> 
>     //提供播放接口
>     public void Play(AudioSource audioSource)
>     {
>         audioSource.clip = audioClip;//替换audioSource里的clip
> 
>         audioSource.Play();
>     }
> }
> ```
>
>