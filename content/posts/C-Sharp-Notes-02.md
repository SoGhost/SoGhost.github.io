---
title: C# _语言基础
date: 2018-07-27T08:18:12+08:00
lastmod: 2018-07-27T08:18:12+08:00
cover: "/img/default6.jpg"
draft: false
categories: ["C#"]
tags: ["C#"]
description: 
---

# 值类型

> <font color=#008080>包括自定义struct类型，enum枚举类型。值类型存储在内存的栈上面，在堆上面存储具体值。</font>
>
> 值类型继承于System.ValueType，所有的类继承于System.Object。(Object是所有类型的基类)
>
> **///	注释——为解释方法，类，空间...进行说明**

- struct——类型

>用于打包封装小的数据集，可以(像class)建方法、事件...。

- enum——类型

>用于限定变量可能性。

```c#
#namespace test
{
    class Program
    {	//角标从0开始，Mon=1，使强制从一开始
        //enum Days : byte	:short 转换类型
        enum Days { Mon, Tues, Wenes, Thurs, Fri, Satur, Sun}//整数型
        struct Perosn
		{
    		public int age;
    		private string name;
    		internal string fname;//命名空间内可以访问
    		protected string lastName;//protected 只有继承于它，才能访问		
        }
        
		static void Main(string[] args)
		{
			Person perosn = new Perosn();
			person.age = 10;
			Console.WriteLine(person.age);
            
            var day = Days.Thurs;
            Console.WriteLine(day);//Thurs
		}
    }
}
```

------

# 引用类型

> 内置引用类型：object, string, dynamic。
>
> 

```c#
static void Main(string[] args)
		{
			object o1 = new object();
			object o2 = new Object();
    		Console.WriteLine(o1.GetType());//获得类型
    		Console.WriteLine(o2.GetString());//转化成字符串
    		//	↑ 都显示 System.Object 类型
    
    		string s1 = "abc";
    		string s2 = "ab";//创建时内存空间已经固定
    		s2 += "c";//较大消耗资源，重新分配内存空间
    		Console.WriteLine(s1);//abc
    		Console.WriteLine(s2);//输出 abc
    		Console.WriteLine(s1 == s2);//返回bool
    		char c = s1[2];//s1 2角标值 赋给c (角标0开始)
    		Console.WriteLine(c);//输出 c
    		string at = @"C:\ABC\abc.cs";//前加@ 忽略\特殊符号，直接输出原有字符
    		Console.WriteLine(at);//输出 C:\ABC\abc.cs
    		string noAt = "C:\ABC\abc.cs";//不加@ 则每个\ 前加\
    		Console.WriteLine(noAt);
    		//string常用方法
    		at.Contains("abc");//是否包含 abc 字符
    		Console.WriteLine(at.Length);//显示长度
    		Console.WriteLine(at.IndexOf("ab"));//查找字符第一次出现的位置
    		//使用string需要增减等字符操作时，使用StringBuilder类型
    		StringBuilder builder = new StringBuilder();//长度不固定
    		//StringBuilder常用方法
    		builder.Append("a");
    		builder.Append("bc");
    		Console.WriteLine(builder);//输出 abc
    		builder.AppendFormat("hello {0}", "abc");
   			Console.WriteLine(builder);//输出 hello abc
		}
```

------

## class

> 包括方法、属性、索引、事件、成员变量、构造函数......

```c#
static void Main(string[] args)
{
	var person = new Person(5);//实例化class，需要构造函数
    Console.WriteLine(person.GetAge());//输出5
    Console.WriteLine(person.Age);//有get 能输出
    person.Age = 10;//有set 能设置
    Console.WriteLine(person.Age);//set 输出0
}

class Person//默认 internal
{
    int age;
    
    public int Age// 属性创建
    {	//// get;	//可以加修饰符
        //// set;
        get{
            return age + 10;
        }
        set{
            age = value - 10;//5 = value - 10;
        }
    }
    
    //class自带一个默认构造函数,但什么也不做
    public Person(int myAge)
    {	//this在class里，可以忽略
        this.age = myAge;//this表当前class实例化对象
    }
    
    public int GetAge()//默认 private,class 内部才能访问
    {
        return age;
    }
    
    public static int GetFive()//静态方法存储在class内部，用Person调用
    {
        return age;
    }
}
```

------

## interface

> 只能包括方法、属性、索引、事件。

```c#
class Person : ISuper//继承这个接口，实现方法
{
    public int GetSuper()
    {
        return age + 100;
    }
}
//interface 接口
interface ISuper//通常大写'I'开头命名
{
	
	int GetSuper();//定义方法，返回值类型int,没有具体实现。具体在class中实现
}

```

------

## abstract class

> （ abstract class Test ）抽象类 不能实例化。
>
> 用于被其它类继承，去实现其中的方法。

```c#
class Person : Test, ISuper//类只能继承1个，但接口多个
{
    Console.WriteLine(GetName());//非抽象方法，可以用
    
    public int GetSuper()//实现抽象方法
    {
        return age + 100;
    }
    
    public override int GetAbstract()//实现抽象方法,编译才不会报错
    {
        return 50;
    }
}

abstract class Test
{
    public string name;
    public string GetName()//非抽象方法
    {
        return name;
    }
    
    public abstract int GetAbstract();//抽象方法
}

interface ISuper
{
	int GetSuper();
}

```

------

# 类型转换

## 隐式转换

> 没有特别语法要求，从小范围转换为大范围类型

 ```c#
 int i = 10;
 long l = i;
 C1 c1 = new C2();
 
 class C1
 {}
 class C2 : C1
 {}
 ```

------

## 显式转换

> 会引起数据丢失，抛错。

```c#
double d = 10.5;
in iFormD = (int)d;
C1 c1 = new C1();
C2 c2 = (C2)c1;

class C1
{}
class C2 : C1
{}
```

------

## 其它转换

```c#
//通过方法进行转换
// int ——> string
string sFormI = i.ToString();//ToString()继承于Object，所有类都有这个方法
//把int的值转换成为 字符串 

// string ——> int
int iFromS1 = Convert.ToInt32("100");//格式必须为 int，否则报错
int iFromS2 = Int32.Parse("101")；//同样格式必须为 int，否则报错
int iFromS3;
bool succeed = Int32.TryParse("abc", out iFromS3);//尝试取转换，不报错，返回bool
ConsoleWritLine(iFromS3);//不会赋值,默认为整形0
//也可以用 接口、类，实现自己的；类型转换。
```

------

## 装箱和拆箱

>装箱：把**值**类型转换为**引用**类型。把值类型打包放到内存堆上，栈上只留一个引用。

>拆箱：把**引用**类型转换为**值**类型。

```c#
//装箱 把值类型打包放到内存堆上，栈上只留一个引用
int iBoxing = 100;
object iBoxed = iToBoxing;//装箱 就是一个隐式转换
//因为所有数据类型都是 bSystem.Object 子类

//拆箱 在内存开辟空间，把值放进去
int iUnBoxing = (int)iBoxed;//显式转换，可能会有数据丢失或出错
object nullObject = 5;
int iNull = (int)nullObject;
。。。。。。
```

------

# 逻辑语句

> 如果 &&	||，&	|使用单个会前后都计算一遍，出错会抛出异常。

```c#
//条件语句
ten = 101;
int ifelse = ten < 100 ? ten : 99;//输出99，if() else 简便写法

//循环语句
int i, j = 10;
for(i = 0,ConsoleWritLine("Start:{0}", i); i < j;
    i++, j--, ConsoleWritLine("i={0}, j={1}", i, j))
    //初始化条件每次都执行，可以执行多个语句；判断条件；循环最后执行
{
    ConsoleWritLine("Body of the loop");
}

bool stop = false;
for(; !stop; )//for(; ; ) 会无限循环
{
	stop = true;
    ConsoleWritLine("停不下来！");
}

//使用foreach() 这个集合必须实现了IEnumerable接口
var listInt = new List<int>() { 1, 2, 3 };
foreach() 必须 (var intInlist in listInt)
    //foreach每次循环提取一次listInt中一个元素，赋给int intInlist
{
    ConsoleWritLine(intInlist);
}

//do while
//先执行一次，其中可以加break, continue 跳出

#regiom//收缩代码块
...代码
#endregiom//收缩代码块
```

------

# 集合类型

## 数组

```c#
//System.Array
//二维数组
string[,] names = new string[5,4];//5*4矩形

//数组初始化简便写法
int[] num1 = new int[5] { 0, 1, 2, 3, 4, 5 };
int[] num2 = new int[] { 0, 1, 2, 3, 4, 5 };// 5可以省略
int[] num3 = { 0, 1, 2, 3, 4, 5 };//之前都可以省略

string[,] names2 = {{"a", "b"}, {"c", "d"}};//多维数组简单的写法

int[][] names3 = { new int[] { 1, 2}, new int[]{ 4} }//数组的数组最简单写法
Console.WriteLine(names2[2]);//获取第三位 索引是2
Console.WriteLine(names2.Length);//获取长度
foreach (int i in names2)//遍历数组用foreach
{
    Console.WriteLine(names2[i]);
}

//数组的数组最简单写法
byte[][] score = new byte[5][];//非矩形 长度不固定

for(int i= 0; i < score.Length; i++)//初始化
{	//给每一个维度附上一个数组
    score[i] = new byte[i + 3];//第一个维度赋给3维数组...
}

for(int i= 0; i < score.Length; i++)
{
    Console.WriteLine("Length of row {0} is {1}", i, score[i].Length);
}
//Length of row 0 is 3
//Length of row 1 is 4
//Length of row 2 is 5
//Length of row 3 is 6
//Length of row 4 is 7
```

------

## ArrayList 和 List

> ArrayList：数组列表存储的都是Object，容易出问题。

```c#
//System.Collection
using System.Collection//需要使用System.Collection
class Program
{
	ArrayList a1 = new ArrayList();
	a1.Add(5);
	a1.Add(100);//添加
	a1.Remove(5);//删除
	a1.Add("string");

	foreach (var e in a1)
	{
 		   Console.WriteLine(e);//输出100，5被Remove
	}
	Console.WriteLine(a1[0]);//通过索引访问，输出100

	var list = new List<int>();//只能存储int
	list.Add(50);
	list.AddRange(new List[] {501, 502});//添加一串元素，类型实现IE接口的都能添加
	list.Contains(200);//是否包含一个数值
	list.Remove(50);
	list.Insert(1, 50);//位置插入
}
```

------

## Hashtable 和 Dictionary

> Hashtable（哈希表）同样没有强制规定类型，容易出错。
>
> 通过Key 访问 Value。
>
> Dictionary 是强类型

```c#
Hashtable ht = new Hashtable();
ht.Add("first", "1");//ht.Add(Key, Value);
ht.Add("second", "2");
ht.Add(100, 1111);
Console.WriteLine(ht["second"]);//输出 2
Console.WriteLine(ht[100]);//输出 1111
Console.WriteLine(ht[99]);//输入不存在key，会返回null,不报错

Dictionary<string, string> d = new Dictionary<string, string>();
d.Add("first", "1");
d.Add(100, 1111);//会报错
Console.WriteLine(ht[99]);//输入不存在key，会报错

{	d.Add(2, 8);
	d.Add(1, 4);
	d.Add(8, 10);	}//自动按key排序

// stack：数据类型，堆栈数据集合，先进后出。
// queue：队列，先进先出。
```

------

# 面向对象——封装

> **封装：**把内容包括在一起控制访问权限，主要通过访问修饰符实现。

> **访问修饰符：**public, private, internal, protected, internal protected

> ***在一个程序集中可以有多个命名空间，引用时使用 using AnotherNameSpace; 即可。***

> **internal：**在程序集内都能被调用(.exe或.dll)。

> **protected**：本身或继承它的class中可见。

> **internal protected：**程序集或继承类可见。

```c#
// private 不希望被外部修改，访问
private int age;
//通过方法，属性访问
public int age
{
	get { return age;}
	set { age = value;}
}
public int GetAge()
{
	return age;
}

//简单重构和优化代码
public int GetAge()
{
    if(CheckAge)
        return age;
	return -1;
}
public bool CheckAge()
{
    if(age <= 0)
        return false;
    return true;
}
```

------

# 面向对象——继承

> 重用旧class方法，也可以使用自己方法。（不能多重继承）

```c#
class Program
{
    static void Main(strin[] args)
    {
        Dog dog = new Dog();
        dog.Age = 10;
        Dog.Bite();//被重写,输出Dog Bite
        dog.GetAge();//输出10
    }
}

class Animal
{
	public int age
	{
		get;
		set;
	}
    
    public virtual void Bite()// virtual被继承中可重写
    {
        Console.WriteLine("Animal Bite");
    }
    
    public virtual void GetAge()//子类不声明override也可以
    {
        Console.WriteLine(Age);
    }
    
    public void BitMan()//无virtual 也能被子类调用
    {
        Console.WriteLine("Bit Man");
    }
}

sealed class Dog//类前有sealed，为不可被继承类

class Dog : Animal
{
	public override void Bite()// override重写父类方法
    {
    	Console.WriteLine("Dog Bite");//被重写
    }
    
	public new void BitMan()//父类有重名，会警告，最好new
    {	//父类方法在子类隐藏，可以写同名方法。
        Console.WriteLine("Bit Man");
    }
}
```

------

**继承实例**

```c#

```

------

# 面向对象——多态

## 静态多态

>静态多态：编译时实现

```c#
class Program//对方法的重载
{
    static void Main(string[] args)
    {
        PrintHello();
        PrintHello("word");
    }

    public static void PrintHello()//同名参数不同
    {
        Console.WriteLine("Hello");
    }

    public static void PrintHello(string toWho)//同名参数不同
    {
        Console.WriteLine("Hello {}", toWho);
    }
}
```

------

```c#
class Program//对运算符的重载
{
	static void Main(string[] args)
	{
		var c1 = new Complex();
		var c2 = new Complex();
		c1.Number = 2;
		c2.Number = 3;
		Console.WriteLine((c1 + c2).Number);//输出5
    }
}

class Complex
{
	public int Number { get; set; }
    
    //必须是static 返回值Complex 关键字operator(运算符) +运算符(变量A, 变量B)
    public static Complex operator +(Complex c1, Complex c2)
    {
        Complex c = new Complex();//新建变量C
        c.Number = c1.Number + c2.Number;
        return c;//返回C
	}
}
```

------

## 动态多态

>动态多态：运行时实现

```c#
class Program//通过override实现多态
{
	static void Main(string[] args)
	{
		Human h1 = new Man();
		Human h2 = new Woman();
		h1.CleanRoom();// Man Clean Room Slowly
		h2.CleanRoom();// Woman Clean Room Quickly
		h1.Number = 3;
		Console.WriteLine(h1);//输出 3
    }
}

class Human
{
    public virtual void CleanRoom()
    {
		Console.WriteLine("Human Clean Room");
	}
    
    public int Number{ get; set; }
    
    public override void ToString()
    {
        return Number.ToString();//也可以重载方法
    }
}

class Man : Human
{
    public override void CleanRoom()
    {
		Console.WriteLine("Man Clean Room Slowly");
	}
}

class Woman : Human
{
    public override void CleanRoom()
    {
		Console.WriteLine("Woman Clean Room Quickly");
	}
}
```