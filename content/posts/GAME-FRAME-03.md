---
title: WWW封装——游戏框架-03
date: 2018-08-29T15:15:31+08:00
lastmod: 2018-08-29T15:15:31+08:00
cover: "/images/default10.png"
draft: false
categories: ["GAME-FRAME"]
tags: ["RPG"]
description: http WWW模块封装
---

# WWW类

> WWW：
>
> 1. **http**请求：超文本协议，点击一次，请求一次数据，返回一个数据（**json、xml、protbuffer...**）。
>
>    - Get请求：参数在**连接**里面，链接的 **？、# 或其他符号**后都是参数。
>    - Post请求：参数在**表单**里面，服务器在底层看见Post请求，会自动分配一个大空间（传输大文件）。
>
> 2. WWW下载本地文件：根据不同平台，有不同前缀
>
>    - IOS：**http://**, **https://** and **file://**
>    - Android：**jar:file://**
>    - PC：**File:///**
>
>    ```c#
>    using System.Collections;
>    using UnityEngine;
>    
>    public class TestWWW : MonoBehaviour{
>    
>        /// <summary>
>        /// Get请求
>        /// </summary>
>        /// <param name="url"></param>
>        /// <returns></returns>
>        public IEnumerator SendGet(string url)
>        {
>            for (int i = 0; i < 1000; i++)
>            {
>                //每次执行后都延时0.2s
>                yield return new WaitForSeconds(0.2f);
>            }
>    
>            WWW www = new WWW(url);
>    
>            //没成功下载继续等待，不往下运行
>            yield return www;
>    
>            //判断下载是否有错
>            if (string.IsNullOrEmpty(www.error))
>            {
>                Debug.Log("Finish==" + www.text);//把下载文件当成text打印出来
>            }
>        }
>    
>        /// <summary>
>        /// Post请求
>        /// </summary>
>        /// <param name="url"></param>
>        /// <returns></returns>
>        public IEnumerator SendPost(string url,WWWForm form)
>        {
>            //参数url 和 WWW表单（form）
>            WWW www = new WWW(url,form);
>    
>            yield return www;
>        }
>    
>        /// <summary>
>        /// WWW下载本地文件
>        /// </summary>
>        /// <param name="url"></param>
>        /// <returns></returns>
>        public IEnumerator DownloadLocalFile(string url)
>        {
>            WWW www = new WWW(url);
>    
>            yield return www;
>    
>            if (string.IsNullOrEmpty(www.error))
>            {
>                Debug.Log("Finish==" + www.text);
>            }
>        }
>    
>        /// <summary>
>        /// 根据不同平台 给url加不同前缀
>        /// </summary>
>        /// <param name="url"></param>
>        /// <returns></returns>
>        public string InitialUrl(string url)
>        {
>            //WindowsEditor在编辑器内 WindowsPlayer是Windows
>            if (Application.platform == RuntimePlatform.WindowsEditor ||
>                Application.platform == RuntimePlatform.WindowsPlayer)
>            {
>                url = "file:///" + url;
>            }
>            else if(Application.platform == RuntimePlatform.Android)
>            {
>                url = "jar:file://" + url;
>            }
>            else
>            {
>                url = "file://" + url;
>            }
>    
>            return url;
>        }
>    
>        void Start(){
>    
>            //Get请求
>            //kun.show.ghostry.cn测试网站
>            string getUrl = "http://kun.show.ghostry.cn/?int=5";
>    
>            //启动协程 Get请求
>            StartCoroutine(SendGet(getUrl));
>    
>            /**************************************************/
>            
>            //Post请求 参数必须放表单里
>            string postUrl = "http://kun.show.ghostry.cn";//链接返回ok
>    
>            WWWForm tmpForm =new WWWForm();
>    
>            //表单添加，参数 + 值
>            tmpForm.AddField("int", 5);
>    
>            //启动协程 Post请求
>            StartCoroutine(SendPost(postUrl, tmpForm));
>    
>            /**************************************************/
>    
>            string url = Application.dataPath + "/www/TestWWW.cs";
>    
>            Debug.Log("转换前=" + url);
>            url = InitialUrl(url);
>    
>            Debug.Log("转换后=" + url);
>            StartCoroutine(DownloadLocalFile(url));
>        }
>    }
>    ```
>
>    ------
>
>
>
> ## 各种路径：
>
>    1. **Application.dataPath：**工程路径，只读，只能在PC上使用。
>
>    2. **Application.persistentDataPath：**用文件系统（File API & StreamReader & StreamWriter）可读可写。
>
>       > PC：临时缓存路径---C:\Users\xxx\AppData\LocalLow\Unity
>       >
>       > Android：外置储存卡---SD卡
>       >
>       > IOS：Document
>
>    3. **Application.streamingAssetsPath：**在Assets下有个streamingAssets文件夹，此路径下所有文件都会打包到移动端（Android APK）上，且只能用WW读取，不能写。
>
> ------
>
>
>
> ## WWW下载
>
>    ```c#
>    using System.Collections;
>    using UnityEngine;
>    
>    /// <summary>
>    /// 1. 任务基类 抽象所有特性
>    /// </summary>
>    public class WWWItem : MonoBehaviour{
>    
>        public virtual void BeginDownLoad()
>        {}
>    
>        public virtual void DownLoadFinish(WWW www)
>        {}
>    
>        public virtual void DownLoadError(WWWItem tmpItem)
>        {}
>    
>        //私有URL 想要被访问 写个get \ set构造器
>        private string URL
>        {
>            get
>            {
>                return URL;
>            }
>    
>            set
>            {
>                URL = value;
>            }
>        }
>    
>        public IEnumerator DownLoad()
>        {
>            BeginDownLoad();
>    
>            WWW www = new WWW(URL);
>    
>            yield return www;
>    
>            //如果下载完成
>            if (string.IsNullOrEmpty(www.error))
>            {
>                DownLoadFinish(www);
>            }
>            else
>            {
>                DownLoadError(this);//如果出错了，把任务返回给上层
>            }
>        }
>    }
>    
>    /// <summary>
>    /// 2. 下载队列
>    /// 如果先添加先下载，则符合先进先出，需要队列
>    /// </summary>
>    public class WWWHelper : MonoBehaviour {
>    
>        public static WWWHelper instance;
>    
>        Queue<WWWItem> allTask;//容纳所有下载任务
>    
>        bool IsDownLoading = true;//是否下载完成
>    
>        void Start()
>        {
>            instance = this;
>    
>            allTask = new Queue<WWWItem>();
>        }
>    
>        public void AddTask(WWWItem items)
>        {
>            allTask.Enqueue(items);//将任务放入队列
>    
>            //如果没有下载完成就不下载
>            if (allTask.Count == 1 && IsDownLoading)
>            {
>                IsDownLoading = false;//下载没有完成
>    
>                StartCoroutine(DownLoadItems());
>            }
>        }
>    
>        public IEnumerator DownLoadItems()
>        {
>            //队列有任务 就去下载
>            while (allTask.Count > 0)
>            {
>                //将队列 头部任务拿出来
>                WWWItem items =  allTask.Dequeue();
>    
>                //相当于for 套用 for （调用另一个协程）
>                yield return items.DownLoad();
>            }
>    
>            IsDownLoading = true;
>        }
>    }
>    
>    public class WWWTxt : WWWItem
>    {
>        public WWWTxt(string url)
>        {
>            Initial(url);
>        }
>    
>        //本地文件 需要修饰前缀
>        public void Initial(string url)
>        {
>            if (Application.platform == RuntimePlatform.WindowsEditor ||
>        Application.platform == RuntimePlatform.WindowsPlayer)
>            {
>                url = "file:///" + url;
>            }
>            else if (Application.platform == RuntimePlatform.Android)
>            {
>                url = "jar:file://" + url;
>            }
>            else
>            {
>                url = "file://" + url;
>            }
>        }
>    
>        public override void BeginDownLoad()
>        {
>            Debug.Log("开始下载");
>        }
>    
>        public override void DownLoadError(WWWItem tmpItem)
>        {
>            //下载出错后 继续可以添加任务
>            WWWHelper.instance.AddTask(tmpItem);
>        }
>    
>        public override void DownLoadFinish(WWW www)
>        {
>            Debug.Log("完成：" + www.text);
>        }
>    }
>    
>    public class TestWWW : MonoBehaviour
>    {
>        if(Input.GetKeyDown(KeyCode.Space))
>        {
>            string url = Application.dataPath + "/www/testwww.cs";
>    
>            WWWTxt tmpText = new WWWTxt(url);
>    
>            WWWHelper.instance.AddTask(tmpText);
>        }
>    }
>    ```
>