---
title: 有限状态机
date: 2018-09-09T09:16:19+08:00
lastmod: 2018-09-09T09:16:19+08:00
cover: "/images/default4.png"
draft: false
categories: ["FSM"]
tags: ["FSM"]
description: 实现有限状态机
---

**有限状态机（Finite-state machine，FSM）：**

> 又称有限状态自动机，简称状态机，是表示有限个状态以及在这些状态之间的转移和动作等行为的数学模型。
>
> 1. 动画控制器控制多个状态，用于实现两个状态之间的跳转。
> 2. 0
>
> **动画分成：**动画控制层、位移控制层、数据层、逻辑控制层。
>
> ```c#
> // 示例 FSM
> /// <summary>
> /// 状态共同的特性，抽象为一个基类
> /// </summary>
> public class FSMBase
> {
>     public virtual void OnEnter()
>     {}
>     public virtual void OnStay()
>     {}
>     public virtual void OnExit()
>     {}
> }
> 
> /// <summary>
> /// 管理多个FSMBase
> /// </summary>
> public class FSMManager
> {
>     //数组存储所有状态
>     FSMBase[] allState;
> 
>     #region Initial
> 
>     // #region (Name) 
>     // #endregion，可以折叠中段代码
>     //进一步封装初始化，构造函数
>     public FSMManager(int stateCount)
>     {
>         Initial(stateCount);
>     }
> 
>     //初始化封装
>     public void Initial(int stateCount)
>     {
>         allState = new FSMBase[stateCount];
>     }
> 
>     #endregion
> 
>     // byte 范围（0~255），节省内存，用于表示状态数足够了
>     // sbyte 可以带符号 标记
>     sbyte curState = -1;//表示当前存储了多少状态
> 
>     sbyte stateIndex = -1;//表示当前处于状态
> 
>     //添加状态
>     public void AddState(FSMBase tmpBase)
>     {
>         //防止越界
>         if (curState > allState.Length - 1)
>             return;
> 
>         curState++;
> 
>         allState[curState] = tmpBase;
>     }
> 
>     //切换状态
>     public void changeState(sbyte index)
>     {
>         // 1. 判断是否越界，否全部跳过不用 2 进行判断
>         if (index > allState.Length - 1 || index == -1)
>             return;
> 
>         // 2. 前面如果有状态则调用，没有跳过
>         //if (index > allState.Length - 1)
>         //  return;
>         //
>         //if (stateIndex != -1)
>         //{
>         //    //当前有状态，则退出
>         //    allState[stateIndex].OnExit();
>         //}
> 
>         stateIndex = index;
> 
>         //更改当前状态为index，并开始执行更换
>         allState[stateIndex].OnEnter();
>     }
> 
>     //一直在update
>     public void Stay()
>     {
>         //初始化后才能调用
>         if (stateIndex != -1)
>             allState[stateIndex].OnStay();
>     }
> }
> ```

------

**动画：**

> 1. 关节动画：多个物体组成一个整体，物体之间有间隙。
>    - MeshFilter：要显示的模型顶点数据。
>    - MeshRender：将MeshFilter筛选出来的顶点数据传递给GPU。
> 2. 蒙皮动画：由顶点将骨骼包围成一个无缝整体。
>    - SkinMeshRender：显示那个模型，将模型文件传给GPU。
> 3. 顶点动画：对模型的顶点位置变化进行制作动画。
>
> 状态机动画：
>
> - Generic：非人形动画
> - Humanoid：人形动画
>
> 通常模型一个fbx，动画一个fbx（人形动画是通用）。
>
> 通常为原地动画，代码控制位移（动画选项里可以禁止位移、OnAnimatiorMove()代替面板去代码控制动画）。

------

**Animator：**

> ```c#
>     // charController.Move()不带重力移动
>     // charController.SimpleMove()带重力移动
>     // animator.deltaPosition表示应用动画片段的位移（如果动画自带位移才会移动）
>     // animator.deltaRotation表示应用动画片段的位移
>     // animator.rootPosition表示应用动画根骨骼的位移
> 	Has Exit Time：动画退出时间，动画没播放完整不跳转，即使达到条件。想要立刻更换状态，则不勾选。
> 	Can Transition To Self：Any State能否传递给自己（机械动作...）。
> ```
> Any State：表示当前的动画状态（可以指向任意状态），如果多个状态需要两两结合，则**只需用Any State指向其它的状态即可**。添加**parameter**为一个**Int**值，给转向的动画设置Int值的Equals为对应整数序号，从而实现指向多个状态（animator.SetInteger("Index", 2)）。
>
> Parameters（参数面板）：
>
> > 1. 1
> > 2. 2
> > 3. 3
> > 4. 4

------

**FSM示例**

1. PlayerCtrl

```C#
using UnityEngine;

public class PlayerIdle : FSMBase
{
    Animator animator;

    public PlayerIdle(Animator tmpAmnimator)
    {
        animator = tmpAmnimator;
    }

    public override void OnEnter()
    {
        animator.SetInteger("Index", 1);
    }
}

public class PlayerWalk : FSMBase
{
    Animator animator;

    public PlayerWalk(Animator tmpAmnimator)
    {
        animator = tmpAmnimator;
    }

    public override void OnEnter()
    {
        animator.SetInteger("Index", 2);
    }
}

public class PlayerRun : FSMBase
{
    Animator animator;

    public PlayerRun(Animator tmpAmnimator)
    {
        animator = tmpAmnimator;
    }

    public override void OnEnter()
    {
        animator.SetInteger("Index", 3);
    }
}

public class PlayerAttack : FSMBase
{
    Animator animator;

    PlayerCtrl playerCtrl;//playerCtrl拿到指针

    public PlayerAttack(Animator tmpAmnimator, PlayerCtrl ctl)
    {
        animator = tmpAmnimator;

        playerCtrl = ctl;//引用PlayerCtrl类
    }

    public override void OnEnter()
    {
        animator.SetInteger("Index", 4);

        isPlayPaticle = false;
        timeCount = 0;
    }

    float timeCount = 0;

    bool isPlayPaticle = false;

    public override void OnStay()
    {
        //一直在调用
        timeCount += Time.deltaTime;

        // 0.15f为需要动画do something的时间
        if (timeCount > 0.15f && !isPlayPaticle)
        {
            isPlayPaticle = true;
            Debug.Log("Is Attack!");
        }
        else if (timeCount > 1.0f)//大于1s切换状态
        {
            // 1. 引用 控制器方法，降低耦合性
            // 2. 委托

            playerCtrl.ChangeState((sbyte)PlayerCtrl.AnimationEnum.Ilde);//切换到Idle状态
        }
    }

    public override void OnExit()
    {
        Debug.Log("Exit!");
        isPlayPaticle = false;
        timeCount = 0;
    }
}

public class PlayerJump : FSMBase
{
    Animator animator;

    public PlayerJump(Animator tmpAmnimator)
    {
        animator = tmpAmnimator;
    }

    public override void OnEnter()
    {
        animator.SetInteger("Index", 5);
    }
}

public class PlayerCtrl : MonoBehaviour
{
    FSMManager fsmManager;

    PlayerModel playerModel;//负责数据

    PlayerController playerController;//负责行走移动

    //体现可扩展性
    public enum AnimationEnum
    {
        Ilde,
        Walk,
        Run,
        Attack,
        Jump,
        Max
    }

    void Start()
    {
        fsmManager = new FSMManager((int)AnimationEnum.Max);

        playerModel = new PlayerModel();

        Animator animator = GetComponent<Animator>();

        PlayerIdle tmpIdle = new PlayerIdle(animator);
        fsmManager.AddState(tmpIdle);

        PlayerWalk tmpWalk = new PlayerWalk(animator);
        fsmManager.AddState(tmpWalk);

        PlayerRun tmpRun = new PlayerRun(animator);
        fsmManager.AddState(tmpRun);

        PlayerAttack tmpAttack = new PlayerAttack(animator, this);
        fsmManager.AddState(tmpAttack);

        PlayerJump tmpJump = new PlayerJump(animator);
        fsmManager.AddState(tmpJump);
    }

    void Update()
    {
        //处于运动状态 一直保持
        fsmManager.Stay();

        if (Input.GetKeyDown(KeyCode.W))
        {
            fsmManager.ChangeState((sbyte)AnimationEnum.Walk);
        }

        if (Input.GetKeyDown(KeyCode.Space))
        {
            fsmManager.ChangeState((sbyte)AnimationEnum.Jump);
        }
    }

    //给状态需要交换是，提供交换API
    public void ChangeState(sbyte stateIndex)
    {
        fsmManager.ChangeState(stateIndex);
    }
}
```

------

2. PlayerModer PlayerController

```c#
public class PlayerModel
{
    int age;

    float healthPoint;
    float attackPoint;
    float manaPoint;

    string headImg;//img路径
}

public class PlayerController
{
    // player move function
}
```

