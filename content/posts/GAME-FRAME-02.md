---
title: 常用设计模式——游戏框架-02
date: 2018-08-22T14:25:14+08:00
lastmod: 2018-08-22T14:25:14+08:00
cover: "/images/default3.png"
draft: false
categories: ["GAME-FRAME"]
tags: ["RPG"]
description: 设计模式
---

# 设计模式

## 单例模式

>单例模式：在软件生命周期，有且只有一个实例（不占内存空间）。
>
>```c#
>//class 抽象概念
>//每new一个（实例化）就会占用一块内存
>//相互没有关联
>
>//如果是单例，就可以省去麻烦
>public static Instance;
>
>//以static const修饰为全局变量,只会实例化一次
>static const _Instance;
>```
>
>- 有两种方式：
>
>  1. 继承MonoBehaviour：拖拽挂载脚本相当于new，***一般用在组织框架（模块）的管理器上。***
>  2. 非继承：
>
>  ```c#
>  //非继承MonoBehaviour通用写法
>  public class TestSingl
>  {
>      private static TestSingl instance;
>      
>      public static TestSingl Instance
>  	{
>          get
>  		{
>              if(instamce == null)
>                  instamce = new TestSingl();
>              
>              return instance;
>  		}
>  	}
>  }
>  ```

------

## 工厂模式

> 工厂模式：交给工厂一个任务，就会产出一个产品，无需关心生产过程。
>
> 作用：用于new一些新的对象（生产金币、野怪...）
>
> ```c#
> using UnityEngine;
> using UnityEngine.UI;
> 
> //工厂模式
> public class Factory {
> 
>     Sprite[] allSprites;
>     Image tmpImage;
>     Transform parents;
> 
>     /// <summary>
>     /// 构造函数（构造API）
>     /// </summary>
>     public Factory()
>     {
>         Initial();
>     }
> 
>     /// <summary>
>     /// 载入sprite资源
>     /// </summary>
>     public void Initial () {
>         //Resources.Load<Sprite>("Resource/" + "spriteName.png");
>         allSprites = Resources.LoadAll<Sprite>("testSprite");
> 
>         //Find是深度优先搜索：从上至下、由浅入深逐个遍历寻找(效率非常低，一般不用)
>         //广度优先搜索：先遍历子类在遍历下一层子类
>         parents = GameObject.FindGameObjectWithTag("MainCanvas").transform;
>     }
> 
>     /// <summary>
>     /// 工厂
>     /// 1. 动态创建image
>     /// 2. spriteIndex为sprite图集中的序号
>     /// 3. 考虑限制index输入范围，防止溢出报错
>     /// </summary>
>     /// <returns>返回生产的物体</returns>
>     public GameObject CreateImage(int spriteIndex, Vector3 pos)
>     {
>         // %取余（模）:取模运算符，整除后的余数
>         // index整除n个范围，剩余的数依旧在范围内
>         spriteIndex = spriteIndex % allSprites.Length;
> 
>         //在内存中创建 gameObject
>         GameObject tmpObj = new GameObject
>         {
>             name = spriteIndex.ToString()
>         };
> 
>         //设置父类 parent，第二参数默认为世界坐标，应填false(否则位置不正确)
>         tmpObj.transform.parent.SetParent(parents, false);
> 
>         //先设置父类在排序
>         // FirstSibling按从上到下、由小到大排序
>         // LastSibling按从上到下、由大到小排序
>         tmpObj.transform.SetAsFirstSibling();
>         tmpObj.transform.SetAsLastSibling();
>         if (spriteIndex > 2)//如果名字序号大于2，前两个位置序号不变，其余从大到小往后排
>         {
>             tmpObj.transform.SetSiblingIndex(2);//让其序号一直为2
>         }
> 
>         // 相对于父级的变换的位置(局部位置)。
>         // 如果该变换没有父级，那么等同于Transform.position。
>         // 这意味着虽然Transform.position中的1个单位总是1个单位，
>         // 但Transform.localPosition中的1个单位将按所有父物体的比例缩放。
>         tmpObj.transform.localPosition = pos;
> 
>         //动态加载 image
>         tmpImage = tmpObj.AddComponent<Image>();
> 
>         //添加图片
>         tmpImage.sprite = allSprites[spriteIndex];
> 
>         return tmpObj;
>     }
> }
> 
> 
> /// <summary>
> /// 使用工厂模式
> /// </summary>
> public class UseFactory {
> 
>     Factory mufactory;
> 
>     int tmpCount = 0;
> 
>     void Start()
>     {
>         mufactory = new Factory();
>     }
> 
>     void Update()
>     {
>         if (Input.anyKeyDown)
>         {
>             mufactory.CreateImage(tmpCount, Vector3.right * tmpCount * 60);
> 
>             tmpCount++;//每生产一次，就+1
>         }
>     }
> }
> ```

------

## 观察者模式

> 观察者模式：就是在不断地询问
>
> 当在游戏开发中：
>
> - 在动画播放0.5s后，播放一个音效（特效）。
>
>   都是计时操作，Time累加
>
> ```c#
> using UnityEngine;
> 
> //观察者模式
> public class Obstor : MonoBehaviour{
> 
>     public Animation _animation;
> 
>     private float timeCount = 0;//计时器
> 
>     void Start()
>     {
>         _animation = GetComponent<Animation>();
>     }
> 
>     void Update()
>     {
>         timeCount += Time.deltaTime;//timeCount每秒加1
> 
>         if (timeCount > 0.5f)
>         {
>             timeCount = 0;
> 
>             //do something
>             BigAttackTime();
>         }
>     }
> 
>     /// <summary>
>     /// 通常封装方法
>     /// </summary>
>     public void BigAttackTime()
>     {
>         _animation.Play("attack");
>         
>         timeCount = 0;
>     }
> }
> ```

------

## 代理模式

> 由于观察者模式不断地访问（Update）过于消耗性能，对应有代理模式。
>
> 代理模式：指向方法的指针。
>
> ```c#
> using UnityEngine;
> using UnityEngine.UI;
> using UnityEngine.Events;
> 
> //代理模式
> public class Delegate : MonoBehaviour {
> 
>     Button btn;
> 
>     void Start()
>     {
>         //绑定按钮事件响应
>         btn = transform.GetComponent<Button>();
> 
>         //AddListener 给Button添加监听
>         //AddListener(OnClick)：分配空间在栈上（栈 占用的空间小，电脑缓存大，速度越快，空间有限，尽量少用）
>         //AddListener(new UnityAction(OnClick))：分配空间在堆上（堆 占用的空间大）（定义指针，直接指向方法）
>         //UnityAction：delegate代理
>         btn.onClick.AddListener(new UnityAction(OnClick));
>     }
> 
>     public void OnClick()
>     {
>         Debug.Log("on click");
>     }
> }
> ```
>

>------
>
>```c#
>//代理模式
>
>public class HusBand{
>    
>    Wife myWife;    //声明Wife类，Wife才能调用里面方法。
>
>    public void AddMoney()
>    {
>        myWife.LeadMoney();
>    }
>
>    public void NoticeOne()
>    {
>        Debug.Log("myWife mom say bad word One!");
>    }
>
>    public void NoticeTwo()
>    {
>        Debug.Log("myWife mom say bad word Two!!");
>    }
>}
>
>public class Wife{
>    
>    public delegate void WifeSister();
>
>    public WifeSister wifeSister;   //wifeSister指向一个方法
>
>    public void LeadMoney()
>    {
>
>    }
>
>    public void SayBadWord()
>    {
>        wifeSister();
>    }
>}
>
>public class TestDelegate : MonoBehaviour{
>
>    void Start()
>    {
>        //HusBand类 直接调用 Wife类方法
>        HusBand tmpHusband = new HusBand();
>
>        tmpHusband.AddMoney();
>
>        //Wife类 直接调用 HusBand类方法
>        Wife tmpWife = new Wife();
>
>        // 多播 = 只调用一个方法
>        tmpWife.wifeSister = tmpHusband.NoticeOne;
>
>        // 多播 += 调用多个方法（指针可以指向多个方法）
>        tmpWife.wifeSister += tmpHusband.NoticeOne;
>        tmpWife.wifeSister += tmpHusband.NoticeTwo;
>
>        //不引用HusBand 调用其中方法
>        //若要引用其他类方法，要么声明，要么使用 delegate。
>        //上方调用一个其他类方法时，则调用几次 SayBadWord()方法（也就是其中的指针）。
>        //通过本类 delegate（代理）的指针，实现访问其他类方法（但其他类中要声明本类）。
>        tmpWife.SayBadWord();//调用wifeSister()指针
>    }
>}
>```

------

## 策略者模式

>策略者模式：根据不同的输入，能够得到不同的结果（多态实现）。
>
>```c#
>using UnityEngine;
>
>//Abstrator的基类
>public class AbsBase
>{
>    public float payMoney;
>
>    //计算税务
>    public virtual void CaculateTax()
>    {
>
>    }
>}
>
>public class AbsPerson : AbsBase
>{
>    public override void CaculateTax()
>    {
>        payMoney *= 0.08f;//计算个人税
>    }
>}
>
>public class AbsCompany : AbsBase
>{
>    public override void CaculateTax()
>    {
>        payMoney *= 0.12f;//计算公司税
>    }
>}
>
>public class TestAbstrator : MonoBehaviour {
>
>    //会计
>    public void CaculateTax(AbsBase tmpBase)//参数不同调用不同方法
>    {
>        tmpBase.CaculateTax();
>    }
>
>    private void Start()
>    {
>        AbsPerson tmpPerson = new AbsPerson();
>
>        CaculateTax(tmpPerson);//多态,父类的指针(tmpBase)指向子类的方法
>
>        AbsCompany tmpCompany = new AbsCompany();
>
>        CaculateTax(tmpCompany);
>    }
>}
>```

------

## 建造者模式

>建造者模式：（分树状图）分层一层层往上建。
>
>```c#
>
>```

------

## 中介者模式

> 中介者模式：少引用，使用中介，减少影响（遵守最少知识原则）。
>
> - 攻击判断
>
>   >Pos1 - Pos2 > r1 + r2 相离
>   >
>   >Pos1 - Pos2 = r1 + r2 相切
>   >
>   >Pos1 - Pos2 < r1 + r2 相交
>
>   ```c#
>   using UnityEngine;
>   
>   //MiddleBase父类，写共有方法；子类写特有方法
>   public class MiddleBase
>   {
>       //角色位置
>       public Transform ower;
>   
>       public float hp;
>   
>       public virtual void ReduceHp(float _Hp)
>       {
>           hp -= _Hp;
>       }
>   }
>   
>   public class MiddleNPC : MiddleBase
>   {
>       public override void ReduceHp(float _Hp)
>       {
>           hp -= _Hp * 2;
>       }
>   }
>   
>   public class MiddlePlayer : MiddleBase
>   {
>   
>   }
>   
>   // TestMiddle 第三方中介者
>   public class TestMiddle : MonoBehaviour{
>   
>       //攻击者 被攻击者
>       public void CaculateHp(MiddleBase attacker, MiddleBase attacked)
>       {
>           // deltaPos 攻击者与被攻击者距离
>           // 1. Vector3 deltaPos = attacker.ower.position - attacked.ower.position;
>           // 2. Vector3.Distance();
>           if (Vector3.Distance(attacker.ower.position, attacked.ower.position) < 10)
>           {
>               //小于攻击范围，被攻击者掉血
>               attacked.ReduceHp(10);
>           }
>       }
>   
>       void Start()
>       {
>           MiddleNPC tmpNpc = new MiddleNPC();
>   
>           MiddlePlayer tmpPlayer = new MiddlePlayer();
>   
>           CaculateHp(tmpNpc, tmpPlayer);//NPC攻击玩家
>   
>           CaculateHp(tmpPlayer, tmpNpc);//玩家攻击NPC
>       }
>   }
>   ```

------

## 门面模式

>门面模式（外观模式）：将不同类型的东西放在一起，形成特定功能。
>
>```c#
>using UnityEngine;
>using UnityEngine.UI;
>
>public class TestShop : MonoBehaviour{
>
>    public GameObject redObj;
>    public GameObject greenObj;
>
>    bool isRed = true;
>
>    public void ShowRedLighting()
>    {
>        redObj.SetActive(true);
>        greenObj.SetActive(false);
>    }
>
>    public void ShowGreenLighting()
>    {
>        greenObj.SetActive(true);
>        redObj.SetActive(false);
>    }
>
>    public void OnClick()
>    {
>        isRed = !isRed;
>
>        if (isRed)
>        {
>            ShowRedLighting();
>        }
>        else
>        {
>            ShowGreenLighting();
>        }
>    }
>
>    void Start(){
>
>        Button btn = GetComponent<Button>();
>
>        btn.onClick.AddListener(OnClick);
>    }
>}
>```
>
>

------

## 组合模式

> 组合模式：将相同类型的东西放在一起，形成特定功能。
>
> ```c#
> using System.Collections.Generic;
> using UnityEngine;
> 
> /// <summary>
> /// 1. 坦克
> /// 2. 炮台
> /// 3. 车身
> /// 4. 轮子
> /// 5. 发动机
> /// </summary>
> 
> //tank基类，装载各个部位
> public class TankBase
> {
>     public virtual void WalkFront(){}
>     public virtual void WalkBack(){}
> }
> 
> //子弹
> public class Bullet : TankBase
> {
>     public override void WalkFront(){}
>     public override void WalkBack(){}
> }
> 
> //轮子
> public class TankWheel : TankBase
> {
>     //旋转
>     public override void WalkFront(){}
>     public override void WalkBack(){}
> }
> 
> public class TankEngine : TankBase
> {
>     //旋转
>     //播放声音
>     public override void WalkFront(){}
>     public override void WalkBack(){}
> }
> 
> public class TestCombine : MonoBehaviour{
> 
>     //统领所有部件运动（可用List、数组）
>     List<TankBase> tank;
> 
>    
>     void Start(){
> 
>         // tank装载各个部位
>         tank = new List<TankBase>();
> 
>         Bullet bullet = new Bullet();
> 
>         tank.Add(bullet);
> 
>         TankEngine engine = new TankEngine();
> 
>         tank.Add(engine);
> 
>         TankWheel wheel = new TankWheel();
> 
>         tank.Add(wheel);
>     }
> 
>     void Update()
>     {
>         if (Input.GetKeyDown(KeyCode.W))
>         {
>             for (int i = 0; i < tank.Count; i++)
>             {
>                 tank[i].WalkFront();
>             }
>         }
> 
>         if (Input.GetKeyDown(KeyCode.S))
>         {
>             for (int i = 0; i < tank.Count; i++)
>             {
>                 tank[i].WalkBack();
>             }
>         }
>     }
> }
> ```
>
>

------

## 状态者模式

> 状态者模式（命令者模式）：
>
> ```c#
> 
> ```
>
>