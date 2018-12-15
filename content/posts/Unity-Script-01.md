---
title: Monobehavior事件响应（1）
date: 2018-07-21T15:26:16+08:00
lastmod: 2018-07-21T15:26:16+08:00
cover: "/images/default9.png"
draft: false
categories: ["Unity"]
tags: ["unity"]
description: "脚本的基本知识"
---

# 启动与刷新函数

## 启动

- Reset()
- Awake()

>在游戏开始之前初始化任何变量或游戏状态。 

- Start

> 在任何Update方法之前，只调用一次。

## 刷新

- FixUpdate()

>每个固定帧率帧都会调用此函数。 (用于物理效果)

- Update()

>则每帧调用更新。(用于处理逻辑) 

- LateUpdate()

> 每帧都会调用LateUpdate 。在调用所有Update函数后调用LateUpdate。

# 交互函数

## 1.物理 Physic

#### OnTriggerEnter()

> 当Collider进入trigger时调用

#### OnTriggerStay()

> 当Collider停留trigger时**每一帧**被调用

#### OnTriggerExit()

> 当Collider离开trigger时调用

#### OnaCollisionEnter()

> 当collider/rigidbody触发另一个rigidbody/collider时被调用

#### OnCollisionStay()

> 当collider/rigidbody触发另一个rigidbody/collider时，将会在每一帧被调用

#### OnCollisionExit()

> 当collider/rigidbody停止触发另一个rigidbody/collider时被调用

## 2.输入 Input

## 3.渲染 Rendering

## 4.对象与组件

#### OnEnable()

> 当对象变为可用或激活状态时此函数被调用

#### OnDisable()

> 当对象变为不可用或非激活状态时此函数被调用

#### OnDestroy()

> 当Monobehavior（脚本）被销毁时运行

# Transform

## 1.坐标系

#### 变量

- Right**—>X axis**
- Up**—>Y axis**
- Forward**—>Z axis**
- worldTolocalMatrix
- localToworldMatrix

### 函数方法

- TransformPoint()

> 变换位置从自身坐标到世界坐标

- InverseTransformPoint()

> 变换位置从世界坐标到自身坐标

- TransformDirection()

> 从自身坐标到世界坐标变换方向

- InverseTransformDirection()

> 变换方向从世界坐标到自身坐标

## 2.位移

### 变量

- Position = new Vector3…
- localPosition = new Vector3…

### 函数方法

- Translate()

> 转换位置
>
> transform.Translate(Vector3* Time.deltaTime, Space.World); 

## 3.旋转

1. **基本旋转 Rotate**

### 变量

- eulerAngles = new Vector3…
- localEulerAngules = new Vector3…
- Rotation = new Quaternion…
- localRotation = new Quaternion…

### 函数方法

- Rotate()
- RotateAround()

> 按固定轴旋转

- LookAt()

> 实时看着物体

2. **四元数 Quaternion**

> Quaternion：四元数用于表示旋转。
>
> 它们结构紧凑，不受**<u>万向节死锁</u>**(Gimbal Lock)定的影响，可以轻松插补。Unity内部使用四元数来表示所有旋转。
> 大多数情况下，只需要采用现有的rotation（例如从Transform）并使用它们来构造新的rotation（例如，在两次rotations之间平滑插值）。99％的时间使用的Quaternion函数是：Quaternion.LookRotation，Quaternion.Angle， Quaternion.Euler，Quaternion.Slerp，Quaternion.FromToRotation和Quaternion.identity。
>
> 可以使用Quaternion.operator *将一个rotation旋转到另一个rotation，或通过rotation旋转矢量。 
>
> - 属性
>   1. eulerAngles： 返回旋转的euler角度。 
>   2. normalized：

- Quaternion.RotateTowards(Quaternion from， Quaternion to，float maxDegreesDelta)

>from 向着 to 旋转，旋转速度。

>```c#
>public class ExampleClass : MonoBehaviour {
>    public Transform target;
>    public float speed;
>    
>    void Update() {
>    float step = speed * Time.deltaTime;
>    transform.rotation = Quaternion.RotateTowards(transform.rotation, 
>    											target.rotation,step);
>    }
>}
>```

- Quaternion.LookRotation(Vector3 forward,  Vector3 upwards = Vector3.up)

>始终看着方向 forward 旋转, 定义向上的向量(默认up)。
>
>```c#
>public class ExampleClass : MonoBehaviour {
>    public Transform target;
>    
>    void Update() {
>        Vector3 relativePos = target.position - transform.position;
>        Quaternion rotation = Quaternion.LookRotation(relativePos);
>        transform.rotation = rotation;
>    }
>}
>```

```c#
//物体匀速旋转后朝向特定点
public GameObject target;
Vector3 currentPosition

void Start(){
	Vector3 currentPosition = transform.position;
}

void Update(){
    RoateToTarget(target.transform.position);
}

void RoateToTarget(Vector3 targetPosition){
	Quaternion currentRoation = transform.rotation;//rotation返回Quaternion
	Vector3 direction = (targetPosition - currentPosition).normalized;
	Quaternion targetRotation = Quaternion.LookRotation(direction);
	Quaternion.RotateTowards(currentRoation,targetPosition,
							45*Time.deltaTime);//每秒旋转45°
}
```

- Quaternion.FromToRotation(Vector3 fromDirection, Vector3 toDirection)

> 求出从fromDirection到toDirection的插值，转为rotation。

```c#
public class ExampleClass：MonoBehaviour {
    void Example（）{
        transform.rotation = Quaternion.FromToRotation（Vector3.up，transform.forward）;
    }
}
```

------

## 4.缩放

### 变量

- lossyScale—>Read Only

> 只能看到物体相对于世界坐标的大小

- localScale = new Vector3

> 相对于父物体放大倍数

```c#
public AnimationCurve curve;//动画曲线
float scale;
//曲线动态缩放物体
Void Update(){
    //赋给scale随时间变化的曲线
    scale = curve.Evaluate(Time...);
    transform.localScale = new Vector3(scale,scale,scale);
}
```

## Hierarchy 位置关系

### 变量

- parent

> 改变父级

- root

> 返回层次最高的Transform

- childCount

> 返回子物体数量

 ```c#
void Example(){
	print(transform.childCount);
     }
 ```

### 函数方法

- Find()
- IsChildOf()
- DetachChildren()

> 销毁子父级关系

- GetChild(int index)
- GetSibling()
- SetAsFirstSibling()
- SetAsLastSibling()
- SetSiblingIndex()

# Time

- time

>从游戏开始到现在所用的时间 

- timeSinceLevelLoad

> 以秒计算到最后关卡已经加载完的时间

- deltaTime

> 以秒计算，完成最后一帧的时间

- fixedTime

>以秒计算游戏开始的时间  

- fixedDeltaTime

>以秒计间隔，在物理其他固定帧速率更新  

- maximumDeltaTime

>一帧能获得的最大时间，物理其他帧速率  

- smoothDeltaTime

>一个（几帧的均值）平滑淡出Time.deltaTime  

- timeScale

>传递时间的，这可以减慢运动效果  

- frameCount

>已经传递的帧的总数  

- realtimeSinceStartup

>以秒计算，自游戏开始的实时时间(**暂停也不影响计时**)

- captureFramerate

>时间会在每帧（1.0/captureFramerate）前进，不考虑真实时间 (**加减速效果**)

- unscaledDeltaTime

>以秒计算，完成最后一帧的时间（不计算Timescale）  

- unscaledTime

>游戏开始到现在所用的时间（不计算Timescale）

# Random 随机数

- seed

>设置用于随机数生成器的种子 

- value

> 返回一个随机数，在0.0~1.0之间
>
> ------
>
> ```c#
>     private Quaternion [] rotationValues;
>     private Color[] colorValues;
>     
>     void Start()
>     {
>         rotationValues = new Quaternion[50];//定义50个new rotation
>         colorValues = new Color[50];
>         int i = 0;
>         while (i < rotationValues.Length)//当i < array Length(为当前数组element数)
>         {
>             rotationValues[i] = Random.rotation;
>             colorValues[i] = new Color(Random.value, Random.value, Random.value);
> 
>             Transform child = transform.GetChild(i);//得到此物体的 子类的transform
>             child.rotation = rotationValues[i];
>             //从子物体上 获取Renderer组件 上的material 上的color = 随机颜色
>             child.gameObject.GetComponent<Renderer>().material.color = colorValues[i];
>             i++;
>         }
>     }
> ```

- insideUnitCircle

>返回半径为1的圆内的一个随机点  

- insideUnitSphere

>返回半径为1的球体内的一个随机点  

- onUnitSphere

>返回半径为1的球体表面的一个随机点  

- rotation

>返回一个随机旋转角度  

- rotationUniform

>返回一个随机选择角度（平均分布） 

- float/int Range(min, max);

>返回一个随机float/int数，在min和max之间 

# Mathf

## 基础数学

Ceil：返回大于或等于f的最小整数。 

Floor：返回小于或等于该数的最大整数。  

Round：返回浮点数 f 进行四舍五入最接近的整数。如果数字末尾是.5，因此它是在两个整数中间，不管是偶数或是奇数，将返回偶数。  

Sigh：返回 f 的数字符号。当 f 为正或为0返回1，为负返回-1。  

Clamp：限制value的值在min和max之间， 如果value小于min，返回min。 如果value大于max，返回max，否则返value 

Clamp01：限制value在0,1之间并返回value。如果value小于0，返回0。如果value大于1,返回1，否则返回value 。  

Lerp：基于浮点数t返回a到b之间的插值(**定义时间**)

LerpAngle：基于浮点数t返回a到b之间的角度插值 

>```c#
>	public float start = 0f;
>	public float end = 5f;
>	private	float startTime = 1f;
>
>    void Update () {
>        //Lerp
>        //使物体从start平滑移动至end
>        transform.position = new Vector3(
>            Mathf.Lerp(start, end, Time.time - startTime),
>            transform.position.y,
>            transform.position.z);
>        //LerpAngel
>        //使物体从start平滑旋转至end
>        float current = Mathf.LerpAngle(start, end, Time.time - startTime);
>        transform.eulerAngles = new Vector3(
>            transform.eulerAngles.x,
>            current,
>            transform.eulerAngles.z);
>    }
>```

MoveTowards：改变一个当前值向目标靠近(**定义速度**)

MoveTowardsAngle：改变一个当前值向目标角度靠近 

>```c#
>public float end = 5f;
>public float speed = 1f;
>
>void Update () {
>    //MoveTowards
>    //使物体匀速移动至终点
>    transform.position = new Vector3(
>    		Mathf.MoveTowards(transform.position.x, end, Time.deltaTime * speed),
>             transform.position.y,
>             transform.position.z);
>    //MoveTowardsAngle
>    //使物体匀速旋转至最终角度
>    float current = Mathf.MoveTowardsAngle(transform.eulerAngles.x, end, Time.deltaTime * speed);
>    transform.eulerAngles = new Vector3(current, transform.eulerAngles.y, transform.eulerAngles.z);
>    }
>```

SmoothStep：和lerp类似，在最小和最大值之间的插值，并在限制处渐入渐出 

SmoothDamp：随着时间的推移逐渐改变一个值到期望值  

SmoothDampAngle：随着时间的推移逐渐改变一个角度到期望角度 

![](https://sofuck.top/images/2018年07月23日13时55分26秒.png)

PingPong：让数值t在 0到length之间往返。t值永远不会大于length的值，也永远不会小于0。 返回值将在0和length之间来回移动。

Repeat：循环t值，从来不会比length大，并且从不会小于0，取值在0~length之间。 

> ```c#
> float scale = Mathf.PingPong(Time.time, 2);//往复运动
> float scale = Mathf.Repeat(Time.time,2);//重复运动
> 
> transform.localScale = new Vector3(1, scale, 1);
> ```

GammaToLinearSpace：转换给定值**从伽马到线性**颜色空间。  

LinearToGammaSpace：转换给定值**从线性到伽马**颜色空间。  

PerlinNoise：生成二维柏林噪波。 

> ```c#
>  public Material m;
>  private Vector3 colorSpace;
> 
>     void Start () {
>         m = this.gameObject.GetComponent<Renderer>().material;
> 
>         colorSpace = new Vector3(
>             Random.Range(0f, 10f),
>             Random.Range(0f, 10f),
>             Random.Range(0f, 10f)
>             );
>     }
> 
>     void Update () {
>         float sX = Mathf.PerlinNoise(Time.time, colorSpace.x);
>         float sY = Mathf.PerlinNoise(Time.time, colorSpace.y);
>         float sZ = Mathf.PerlinNoise(Time.time, colorSpace.z);
> 
>         m.color = new Color(sX, sY, sZ);
>     }
> ```

------

## 3D 数学

#### 点积(Dot Product)

> - 二维
>
>   向量点积：a • b = a~x~ × b~x~ + a~y~ × b~y~ = |a| × |b| × cos(θ) = |a| × cos(θ) × |b|
>
>   ***<u>|a| × |b| × cos(90°) = 0</u>***
>
> - 三维
>
>   Vector A (x,y,z)	Vector B (x,y,z)
>
>   (A~x~ * B~x~) + (A~y~ * B~y~) + (A~z~ * B~z~) = Dot Product
>
>   > Vector3.Dot(Vector A, Vector B)
>   >
>   > 直接返回一个点积 float。

#### 叉积(Cross Product)

> A ^ B = C
>
> ![A ^ B = C](https://sofuck.top/images/CrossProduct.png)
>
> ![](https://sofuck.top/images/Snipaste_2018-08-08_13-11-26.png)
>
> ![Cross Product](https://sofuck.top/images/Snipaste_2018-08-08_13-11-03.png)
>
> ![](https://sofuck.top/images/Snipaste_2018-08-08_13-19-46.png)
>
> ![](https://sofuck.top/images/Snipaste_2018-08-08_13-19-55.png)
>
> > Vector3.Cross(VectorA, VectorB)
> >
> > 返回VectorC。
> >
> > 用于判断A向B旋转时，求出旋转轴。

## Vector3 三维向量

- 静态属性(Static Properties)
> | Static Properties | Vector3 |
> | ------- | ---- |
> | back	  | Vector3(0, 0, -1) |
> | down    | Vector3(0, -1, 0) |
> | forward | Vector3(0, 0, 1) |
> | left    | Vector3(-1, 0, 0) |
> | one     | Vector3(1, 1, 1) |
> | right   | Vector3(1, 0, 0) |
> | up      | Vector3(0, 1, 0) |
> | zero    | Vector3(0, 0, 0) |
>

- Properties

> - magnitude：返回这个向量的长度（只读）。
>
>   向量的长度是√(x * x + y * y + z * z)—距离.
>
>   如果只需要比较一些向量的大小，那么可以使用sqrMagnitude等来比较它们的大小的平方(计算squared magnitudes的速度更快)。
>
> - sqrMagnitude：向量的长度 (x * x + y * y + z * z)—距离的平方（只读）。
>
> - normalized：同Static Function—Normalize。
>
> - x： 向量的X分量。 
>
> - y： 向量的Y分量。 
>
> - z： 向量的Z分量。 
>

- Public Methods

> - Equals：如果给定的向量与此向量完全相等，则返回true。 由于浮点不准确，对于基本上（但不完全）相等的向量，这可能返回false。使用***<u>==运算符</u>***测试两个向量的近似相等性。 
> - Set：设置现有Vector3的x，y和z分量。 
> - ToString：输出Vector3的坐标。

- Static Methods

> - Angle：返回from和to之间的角度（以度为单位）。
> - ClampMagnitude：返回向量的副本，其大小被限制为maxLength。 
> - Cross：两个向量的交叉产物。
> - Distance：返回a和b之间的距离。
> - Dot：两个向量的乘积。
> - Lerp：在两个向量之间进行线性插值。
> - LerpUnclamped：在两个向量之间进行线性插值。
> - Max：返回由两个向量的最大分量组成的向量。
> - Min：返回由两个向量的最小分量组成的向量。
> - MoveTowards：将直线上的点电流移向目标点。 
> - Normalize：使此向量的大小为1。
> - OrthoNormalize：使矢量标准化并相互正交。
> - Projects：将矢量投影到另一个矢量上。
> - ProjectOnPlane：将矢量投影到由垂直于平面的法线定义的平面上。
> - Reflects：反射法线定义的平面上的矢量。
> - RotateTowards：向目标旋转矢量电流。
> - Scale：将两个向量分量相乘。 
> - SignedAngle：返回from和to之间的有角度符号角度。
> - Slerp：在两个向量之间进行球面插值。
> - SlerpUnclamped：在两个向量之间进行球面插值。 
> - SmoothDamp：随着时间的推移逐渐将向量更改为所需的目标。


- Static Function

> - Normalize：得到Vector3单位向量。
>
>   Vector3.Normalize = vector1 / 单位向量
>
>   vector1.Normalize();（修改原数为vector1.normalized）
>
>   **【单位向量:<u>1.732051 = √3 = √(1^2^ + 1^2^ + 1^2^)</u>】**
>
- 运算符(Operator)

> - operator -：从另一个向量中减去一个向量。
> - operator !=：如果向量不同，则返回true。
> - operator *：将矢量乘以数字。
> - operator /：用矢量除以矢量。
> - operator +：添加两个向量。
> - operator ==：如果两个向量大致相等，则返回true。