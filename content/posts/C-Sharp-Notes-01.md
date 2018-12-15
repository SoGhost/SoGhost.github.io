---
title: C# _基本操作
date: 2018-07-24T07:43:47+08:00
lastmod: 2018-07-24T07:43:47+08:00
cover: "/img/bg2.jpg"
draft: false
categories: ["C#"]
tags: ["c#"]
description: 
---
# 1.定义变量 必须声明

```c#
//&&逻辑和||逻辑或
class Program
{
	static void (string [] args)
	{
    int a=3;
    int b=0;
    
    if(a==3)
    {
    	  b=4;  
    }
    else if(a==4)
    {
        b=5;
	}
    Console.ReadKey();//获取按键 暂停
	}
}
```

------

# 2.if()

```c#
//随机抽卡
Random random = new Random();
int r = random.Next(0,100);//从0-99
string s = "";

if(r>=0&&r<10)//0-9是关羽
{
    s = "关羽";//0%-10%
}
else if(r>=10&&r<30)
{
    s = "张飞";//10%-30%
}
else if(r>=30&&r<60)
{
    s = "赵云";//30%-60%
}
else if(r>=60&&r<100)
{
    s = "黄忠";//60%-100%
}
Console.WriteLine("您抽到了卡牌"+s);
------------------------------------------------------------------
//逻辑优化
if(r<10)//r肯定大于0 略过
{
    s = "关羽";
}
else if(r<30)//小于10处理过直接 跳过
{
    s = "张飞";
}
else if(r<60)
{
    s = "赵云";
}
else//全部判断完 就剩一组直接else
{
    s = "黄忠";
}
```

------

# 3.while()

```c#
//1=1 true 1!=1 false
int i=1;
while(i<=100)//1-100
{
    Console.WriteLine(i);//回车换行
    
    Console.Write(i+" ");//不换行
    i++;//i+=1;
}
	Console.WriteLine();//打印回车

//题目，从键盘输入一个数，打印其乘以2的结果，然后再次输入，直到用户输入“end“为止
while(true)
{
	string s = Console.ReadLine();//等待用户输入 并回车 输入
    if(s =="end") //if中有break跳出所有循环判断
    	{break;}
	int input = int.Parse(s);//分析字符串整数转为整数,失败跳出
	Console.WriteLine(input * 2);
}
-----------------------------------------------------------------
while(true)
{
	string s = Console.ReadLine();//等待用户输入 并回车 输入
    if(s =="end")
    	{break;}
	int input;
    if(int.TryParse(s,out input))//尝试分析s，out输出到input中，返回bool
    {
        Console.WriteLine(input * 2);
    }
    else
    {
        Console.WriteLine("输入字符错误，重新输入");
        continue;//跳出当前循环，回到while顶部重新开始判断
    }
    Console.WriteLine("------");
}
```

------

# 4.for()

```c#
//打印表格
//1_1 1_2 1_3...
//2_1 2_2 2_3...
//3_1...
int rows = 5;//5行
int cols = 5;//5列
for(int i=0;i<rows;i++)
{
    for(int j=0;j<cols;j++)
	{
    Console.Write(i+"_"+j+" ");
    }
    //在一行打印完后+回车
    Console.WriteLine();
}
```

------

```c#
//打印9x9乘法表
//1x1=1
//2x1=2 2x2=4
//3x1=3 3x2=6 3x3=9
//......
int rows = 9;
int cols = 9;
for(int i=0;i<rows;i++)
{
    for(int j=0;j<cols;j++)
    {
        int a = i+1;//精简
        int b = j+1;
        
        if(a>=b)//第一种-全部判断是否符合条件输出-耗时
        	Console.Write(a+"x"+b+"="+(a*b)+" ");
        
        if(a<b)//第二种-计算量小，直接跳出-节省时间效率高
            break;
        Console.Write(a+"x"+b+"="+(a*b)+" ");
    }
    //因为行列相等，所以👇
    for(int j=0;j<i;j++)//第三种-把if拿到for中判断
    {
        int a = i+1;
        int b = j+1;
        Console.Write(a+"x"+b+"="+(a*b)+" ");
    }
    //第四种-精简代码
    //i j 初始为1，删除 a b
    Console.WriteLine();
}
```

------

# 5.函数

```c#
using System;

namespace Function
{
	class Program
    {
        static int Double(int n)
        {
            return n*2;
		}
        static int Add(int a,int b)
        {
            return a + b;
        }
        static void Say(string s)
        {
            for(int i=0;i<10;i++)
            {
				Console.WriteLine(s);
            }
        }
        static bool IsEvenNum(int n)//是否偶数
        {
            return n % 2 == 0;//%取余数 返回bool
        }
        static void Main(string[] args)
        {
            int n = Double(9);
            Console.WriteLine(n);
            Add(99,n);
            Say("HELLO ");
            
        }
    }
}
```
------

# 6.数组

```c#
//创建数组
//1 增：不可以
//2 删：不可以
//3 改：利用下标修改元素值
//5 查:遍历数组，查找所需值
 
//查找某数是否在数组中
static bool ArryAcontains(int[] arry, int n)
{
    for(int i = 0; i < 5; i++)//👇动态获取数组大小👇
    for(int i = 0; i < array.Length; i++)//Length表示array声明是长度
    {
        if(int[i] == n)
            return true;
	}
    return false;//全部遍历后在进行错误返回
}

//最大数
static int ArrayMax(int[] array)
{
    //在负数中不适用，则不另设数值，另取array中的一个
    //int n = 0;
    int n = array[0];
    for(int i = 0; i < array.Length; i++)//Length表示array声明是长度
    {
        if(array[i] > n)
            n = array[i];//保存Max值
	}
    return n;//遍历后,返回最大值
}

//偶数 乘以 2
static void ArrayDoubleEventNum(int[] array)//引用类型array，改变原先数值
{
    for(int i = 0; i<; i++)
    {
        if((array[i]) % 2 == 0)//取与2的余数判断
            arrry[i] *= 2;
	}
}

static void Main(string[] arge)
{
    int[] array;
    array = new int[5];
    
    array[0] = 10;//全正
    array[1] = 88;
    array[2] = 99;
    
    array[0] = -9;//全负
    array[1] = -56;
    array[2] = -80;
    
    Console.WriteLine("array Acontains:"+ArryAcontains(array, 88));
    Console.WriteLine("array Acontains:"+ArryAcontains(array, 77));
    
    Console.WriteLine("array Max:"+ArrayMax(array));
}
//在 array. 方法里都可以找到
```

------

# 7.面向对象

## **面向对象程序（ OPP ）**

### **文字游戏**

```c#
using System;//文字游戏

namespace OOP
{
    class Role
    {
        public int hp;
        public int attack;

        public Role(int _hp,int _attack)//构造函数 无返回值
        {
            hp = _hp;
            attack = _attack;
        }

        public void BeHit(int const_hp)//被打方法
        {
            hp -= const_hp;
            if (hp < 0)
                hp = 0;
        }
    }
    
    class Program
    {
        static void Main(string[] reges)
        {
            Role player = new Role(100, 10);
            Role monster = new Role(1000, 5);

            while (player.hp >0 && monster.hp>0)
            {
                monster.BeHit(player.attack);
                Console.WriteLine("怪物受到：{0}点伤害,当前血量{1}", player.attack, monster.hp);
                if(monster.hp <= 0)
                    break;
                player.BeHit(monster.attack);
                Console.WriteLine("玩家受到：{0}点伤害,当前血量{1}", monster.attack, player.hp);
                if (player.hp <= 0)
                    break;
            }
            if (player.hp > 0)
            {
                Console.WriteLine("玩家胜利");
            }
            else
            {
                Console.WriteLine("怪物胜利");
            }
            Console.ReadKey();
        }
    }
}
//Console.WriteLine(" {0} {1} {2} ", a, b, c);
```
------

### **多态 - 账本 订单**

***1.基本操作***

```c#
namespace AccountAndOrder
{
    class AccountBook//账本
    {
        float total_price;//总价 = num * price
        OrderItem[] items;//创建数组 方便管理
        int item_index;//订单下标

        public AccountBook()//构造函数 初始化变量
        {
            total_price = 0;
            items = new OrderItem[100];
            item_index = 0;
        }

        public void AddOrderItem(OrderItem  order)
        {
            total_price += order.price * order.num;
            items[item_index] = order;
            item_index++;
        }

        public float GetTotalPrice()
        {
            return total_price;
        }

        public void ShowItems()
        {
            Console.WriteLine("=====账本内容：======");
            for(int i = 0; i < items.Length ; i++)
            {
                if(items[i] == null)
                {
                    continue;
                }
            Console.WriteLine("{0}:{1} {2} {3}", 
                    i, items[i].name, items[i].num, items[i].price);
            }
            Console.WriteLine("================");
        }

    }

    class OrderItem//订单
    {
        public string name;
        public float price;
        public int num;
    }

    class Program
    {
        //给bool随便添加订单
        static void AddOrders(AccountBook book)
        {
            var order1 = new OrderItem();//创建订单1
            order1.name = ("苹果");
            order1.price = 1.0f;
            order1.num = 3;
            book.AddOrderItem(order1);//添加订单

            var order2 = new OrderItem();//创建订单2
            order2.name = ("香蕉");
            order2.price = 0.5f;
            order2.num = 10;
            book.AddOrderItem(order2);

            var order3 = new OrderItem();//创建订单2
            order3.name = ("桃子");
            order3.price = 3f;
            order3.num = 2;
            book.AddOrderItem(order3);
        }
        static void Main(string[] args)//Main用于测试
        {
            var book = new AccountBook();//创建账本
            AddOrders(book);
            book.ShowItems();

            Console.WriteLine(book.GetTotalPrice());
        }
    }
}
```

------

​	***2.正常操作***

```c#
namespace AccountAndOrder
{
    class AccountBook//账本
    {
        float total_price;//总价 = num * price
        OrderItem[] items;//创建数组 方便管理
        int item_index;//订单下标

        public AccountBook()//构造函数 初始化变量
        {
            total_price = 0;
            items = new OrderItem[100];
            item_index = 0;
        }

        public void AddOrderItem(OrderItem order)
        {
            //账本代替订单算完，订单不能自定义算法
            //if (order is OrderItemDiscount)//判断对象是否属于某个类型
            //{
            //    var order_d = new OrderItemDiscount();
            //    total_price += order_d.price * order_d.num * order_d.discount;
            //}
            //else
            //{
            //    total_price += order.price * order.num;
            //}

            total_price += order.GetPrice();//会调用正确的
            items[item_index] = order;
            item_index++;
        }

        public float GetTotalPrice()
        {
            return total_price;
        }

        public void ShowItems()
        {
            Console.WriteLine("=====账本内容：======");
            for (int i = 0; i < items.Length; i++)
            {
                if (items[i] == null)
                {
                    continue;
                }
                Console.WriteLine("{0}:{1} {2} {3}",
                        i, items[i].name, items[i].num, items[i].price);
            }
            Console.WriteLine("================");
        }

    }

    class OrderItem//订单
    {
        public string name;
        public float price;
        public int num;

        virtual public float GetPrice()//可被重载 
        {
            return price * num;
        }
    }
    //继承
    class OrderItemDiscount:OrderItem//打折方法
    {
        public float discount = 0.8f;

        override public float GetPrice()//重写
        {
            return price * num * discount;
        }
    }
    //继承
    class OrderItemFull : OrderItem//满减方法
    {
        public  int full = 5;//满多少
        public float sub = 0.5f;//满full 减sub

        override public float GetPrice()//重写
        {
            float temp = price * num;
            if(temp >= full)
            {
                temp -= sub;
            }
            return temp;
        }
    }

    class Program
    {
        //给bool随便添加订单
        static void AddOrders(AccountBook book)
        {
            // var 处写 OrderItem 或 方法 都可以
            var order1 = new OrderItemDiscount();//创建订单1    打折
            order1.name = ("苹果");
            order1.price = 1.0f;
            order1.num = 3;
            book.AddOrderItem(order1);//添加订单

            var order2 = new OrderItem();//创建订单2    正常
            order2.name = ("香蕉");
            order2.price = 0.5f;
            order2.num = 10;
            book.AddOrderItem(order2);

            var order3 = new OrderItemFull();//创建订单2    满减
            order3.name = ("桃子");
            order3.price = 3f;
            order3.num = 2;
            book.AddOrderItem(order3);
        }
        static void Main(string[] args)//Main用于测试
        {
            var book = new AccountBook();//创建账本
            AddOrders(book);
            book.ShowItems();

            Console.WriteLine(book.GetTotalPrice());

            Console.ReadKey();
        }
    }
```

------

​	***3.模拟器练习***

```c#

```

------

## **List 容器**

```c#
using System;
using System.Collections.Generic;//包含List...容器

namespace ConsoleApp2
{
    class Program
    {
        static List<int> list = new List<int>();//创建List 类似array

        static void InitList(List<int> list)
        {
            list.Clear();//清空List
            list.Add(10);//添加元素 角标0
            list.Add(20);//添加元素 角标1
            list.Add(30);//添加元素 角标2
        }

        static void SetList(int index,int value)
        {
            list[index] = value;
        }

        static void PrintList(List<int> list)
        {
            for (int i = 0; i < list.Count; i++)
            {//list.Count 等同于(array.Length),动态获取lsit长度
                Console.Write(list[i] + " ");
            }
            Console.WriteLine();
        }

        static void Main(string[] args)
        {
            InitList(list);
            PrintList(list);

            SetList(0, 90);
            SetList(3, 80);//不能超过定义大小
            PrintList(list);
            Console.WriteLine("------");

            List<int> l = new List<int> { 1,2,3,4,5,6,7,8,9,10 };//初始化列表
            //l.Remove(6);//删除值为6的数，多次调用按顺序删除
            //l.RemoveAt(7);//删除角标为7的元素
            for(int i=0; i<10; i++)//连续删除10次,会全部删除
            {
                //l.RemoveAt(0);
            }
            
            l.RemoveAt(2);
            l.RemoveAt(2);
            PrintList(l);

            l.Insert(2, 3);//在 2 角标,插入添加 3
            PrintList(l);
            l.Insert(3, 4);//在 3 角标,插入添加 4
            PrintList(l);
        }
    }
}
```

------

```c#
    //List 练习
    // 删除指定角标元素
    // 例如原始list内容P{22，33，44，55，66，77，88}
    // 删除角标为0 ，2 ，3的元素，剩下22，44， 55
    // 然后输出list
    // ！<--将角标从大到小remove即可-->！
    class Program
    {
        static void ListExercise(List<int> list, List<int> remove)
        {                       //原始数据      //要删除的下标list
            //随机序列remove 用排序
            remove.Sort();//可排序元素，先排序
            remove.Reverse();//再倒序
            //可以引入错位量 在删除
            foreach (int index in remove)//直接循环角标对应值
            {
                list.RemoveAt(index); 
            }
            PrintList(list);
            return;
        }

        static void PrintList(List<int> list)
        {
            for(int i = 0; i < list.Count; i++)
            {
                Console.Write(list[i] + " ");
            }
            Console.WriteLine();
        }

        static void Main(string[] args)
        {
            ListExercise(new List<int> { 22, 33, 44, 55, 66, 77, 88 },
                new List<int> { 0, 3, 2 });

            Console.ReadKey();
        }
    }
```

------

## **Dictionary 容器**

```c#
using System;
using System.Collections.Generic;//包含List...容器

namespace ConsoleApp3
{
    class Program
    {
        //专门创建字典函数
        static Dictionary<int, int> CreateDict(int k, int v)
        {
            var dict = new Dictionary<int, int>();
            dict[k] = v;//增加 索引
            return dict;
        }

        static void Main(string[] args)
        {   // 创建string 和 int 字典
            //Dictionary<string, int> dict = new Dictionary<string, int>();
            // var 系统推到出是什么类型 简化变量类型
            var dict = new Dictionary<string, int>();

            // 增
            dict.Add("a", 1);// 1. 增加
            dict.Add("b", 2);
            dict["c"] = 3;   // 2. 增加也可改

            // 删
            dict.Remove("a");//Remove key删除索引

            // 改
            dict["b"] = 99;

            // 直接查找
            Console.WriteLine(dict["c"]);

            // 遍历 foreach   一对Key Value 就是KeyValuePair<string,int>
            foreach (var pair in dict)
            {
                Console.WriteLine(pair.Key + ":" + pair.Value);
            }

            // 问题1. KeyNotFoundExceptoin ,读取不存在Key
            // Console.WriteLine(dict["Y"]);
            // 但是，新建元素可以

            //创建字典函数
            Console.WriteLine("-------");
            var dict2 = CreateDict(66,48925);//增加 索引
            foreach (var pair in dict2)
            {
                Console.WriteLine(pair.Key + ":" + pair.Value);
            }

            Console.ReadKey();
        }
    }
}
```

------

```c#
// 给定一个字典，删除所有字典中Value为偶数的元素

```

------

## **值类型 - 引用类型**

**<font color=#008080>变量类型分为两大类:值类型和引用类型**</font>

><font color=#008080>值类型：int, float, double, bool 等所有基本类型（不包括string）,以及struct结构体类型。</font>

> <font color=#8A2BE2>引用类型：string, 数组,以及所有自定义类型。除了值类型以外都是引用类型。</font>

  <font color=#8A2BE2>**关键区别：**</font>

  1. > 引用类型变量，变量本身包含存储空间。

  2. > 引用类型变量，变量本身只是一个引用，会指向（引用）实际的对象；<u>也可以指向null。</u>

  3. > 等号意义不同，值类型变量用等号直接复制内容，引用类型是修改引用。

```c#
//值类型  b不变
int a = 10;
int b = a;
a = 3;
Console.WriteLine("b" + b);// b = 10

//引用类型
int[] l1 = { 1, 2, 3 };
int[] l2 = l1;
l1[0] = 9;
Console.WriteLine("l2[0]" + l2[0]);// l2[0] = {0, 2, 3}

string s1 = new string "小明"；
string s2 = s1;
s1 = "小强";
Console.WriteLine("s2:" + s2);// s2 = 小强

 
```

------

## **堆 和 栈**

```c#

```

------

## **Delegate 委托**

```c#
namespace DelegateStudy
{
    class Program
    {
        //委托声明的，其实是一个类型
        delegate void GameLevel();

        static void LevelA()
        {
            Console.WriteLine("BOSS：库巴");
        }

        static void LevelB()
        {
            Console.WriteLine("BOSS：瓦利奥");
        }

        static void LevelC()
        {
            Console.WriteLine("BOSS：马里奥");
        }

        static void LevelD()
        {
            Console.WriteLine("BOSS：路易");
        }

        static void LevelE()
        {
            Console.WriteLine("BOSS：XXX");
        }

        static void Main(string[] args)
        {
            List<GameLevel> levels = new List<GameLevel>();
            levels.Add(LevelA);
            levels.Add(LevelB);
            levels.Add(LevelC);
            levels.Add(LevelD);
            levels.Add(LevelE);

            for(int i=0;i<levels.Count; i++)//顺序调用
            {
                levels[i]();//调用方法
            }
    }
}
```

------

