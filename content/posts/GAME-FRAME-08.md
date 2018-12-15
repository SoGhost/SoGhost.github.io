---
title: 角色模块封装——游戏框架-08
date: 2018-09-17T08:58:13+08:00
lastmod: 2018-09-17T08:58:13+08:00
cover: "/images/default1.png"
draft: false
categories: ["GAME-FRAME"]
tags: ["RPG"]
description: 角色封装
---

> Role（角色基类）：
>
> - FSM
>
> - CharactorController
>
> 1. 玩家：
>
>    - EasyTouch 控制移动和旋转
>
>    - 矩形攻击...
>
> 1. NPC：
>       - AI 攻击

------

NPCManager管理NPC

```c#
using System.Collections.Generic;
using UnityEngine;

public class NPCManager : MonoBehaviour
{
    public static NPCManager Instance;

    private Transform player;

    public Transform Player
    {
        get
        {
            if (player == null)
            {
                player = GameObject.FindGameObjectWithTag("Player").transform;
            }
            return player;
        }
    }

    NPCManangerModel npcManagerModel;

    void Awake()
    {
        Instance = this;

        allNPC = new List<NPCCell>();

        npcManagerModel = new NPCManangerModel();
    }

    List<NPCCell> allNPC;//存储所有NPC

    /// <summary>
    /// 工厂模式，生产一个NPC
    /// </summary>
    /// <param name="path">NPC资源路径</param>
    /// <param name="npcParent">NPC Parent</param>
    /// <returns></returns>
    public GameObject FactoryNPC(string path, Transform npcParent)
    {
        //读取Prefab，内存创造一个NPC
        Object tmpObj = Resources.Load(path);

        //实例化NPC
        GameObject tmpNPC = Instantiate(tmpObj) as GameObject;

        //设置父类
        tmpNPC.transform.SetParent(npcParent, false);

        //添加必要组件
        NPCCell tmpCell = tmpNPC.AddComponent<NPCCell>();

        //存入管理
        allNPC.Add(tmpCell);

        return tmpNPC;
    }

    /// <summary>
    /// 玩家释放技能
    /// </summary>
    public void AttackedByPlayer()
    {
        for (int i = 0; i < allNPC.Count; i++)
        {
            NPCCell tmpCell = allNPC[i];

            // RectAttackHudge()矩形攻击检测
            //如果判断NPC在矩形攻击范围内，则攻击
            if (RectAttackHudge(player,tmpCell.transform,npcManagerModel.forwardDistance,npcManagerModel.rightDistance))
            {
                tmpCell.ReduceHealthPoint(npcManagerModel.AttackPoint);//NPC掉血
            }
        }
    }

    /// <summary>
    /// NPC是否跟随移动
    /// </summary>
    public void AIFollowPlayer()
    {
        for (int i = 0; i < allNPC.Count; i++)
        {
            NPCCell tmpCell = allNPC[i];

            //可以适当更改计算间隔
            tmpCell.AIAttack();
        }
    }

    void Update()
    {
        AIFollowPlayer();
    }
}

public class NPCManangerModel
{
    public float forwardDistance = 10;
    public float ForwardDistance
    {
        get
        {
            return forwardDistance;
        }
        set
        {
            forwardDistance = value;
        }
    }

    public float rightDistance = 5;
    public float RightDistance
    {
        get
        {
            return rightDistance;
        }
        set
        {
            rightDistance = value;
        }
    }

    //Player攻击力
    public float attackPoint = 5;
    public float AttackPoint
    {
        get
        {
            return attackPoint;
        }
        set
        {
            attackPoint = value;
        }
    }
}

```

------

PlayerController控制Player

```c#
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

RoleBase所有角色基类

```c#
using UnityEngine;

//如果没有组件则不运行，添加依赖CharacterController组件
[RequireComponent(typeof(CharacterController))]
public class RoleBase : MonoBehaviour
{
    FSMManager fsmManager;

    CharacterController charController;

    public enum AnimationEnum
    {
        Ilde,
        Walk,
        Run,
        Attack,
        Jump,

        Max
    }
    
    public virtual void Initial()
    {
        fsmManager = new FSMManager((int)AnimationEnum.Max);

        charController = GetComponent<CharacterController>();
    }

    public virtual void SimpleMove(Vector3 speed)
    {
        charController.SimpleMove(speed);
    }

    public virtual void ChangeState(sbyte state)
    {
        fsmManager.ChangeState(state);
    }
}

```

------

NPCCell控制NPC

```c#
using UnityEngine;

public class NPCCell : RoleBase
{
    NPCDate npcDate;

    void Awake()
    {
        Initial();
    }

    public override void Initial()
    {
        npcDate = new NPCDate();
    }

    //先找到玩家，在检测是否在范围内
    public void AIAttack()
    {
        //NPC距离玩家小于10，则跟随，小于5攻击
        Vector3 deltaA = NPCManager.Instance.Player.position - transform.position;

        if (deltaA.magnitude < npcDate.FollowDistance)
        {
            if (deltaA.magnitude < npcDate.AttackDistance)
            {
                //开始攻击
                ChangeState((sbyte)AnimationEnum.Attack);

                //掉血
                ReduceHealthPoint(npcDate.attackPoint);
            }
            else
            {
                //开始跟随
                //向玩家方向移动
                SimpleMove(deltaA.normalized * npcDate.MoveSpeed);
            }
        }
    }

    //被攻击 掉血
    public void ReduceHealthPoint(float hp)
    {
        npcDate.healthPoint -= hp;
    }
}

/// <summary>
/// 数据 从配置表 读取
/// </summary>
public class NPCDate
{
    //NPC血量
    public float healthPoint;
    public float HealthPoint
    {
        get
        {
            return healthPoint;
        }
        set
        {
            healthPoint = value;
        }
    }

    //NPC跟随距离
    public float followDistance = 10;
    public float FollowDistance
    {
        get
        {
            return followDistance;
        }
        set
        {
            followDistance = value;
        }
    }

    //NPC攻击范围
    public float attackDistance = 5;
    public float AttackDistance
    {
        get
        {
            return attackDistance;
        }
        set
        {
            attackDistance = value;
        }
    }

    //NPC移动速度
    public float moveSpeed = 2;
    public float MoveSpeed
    {
        get
        {
            return moveSpeed;
        }
        set
        {
            moveSpeed = value;
        }
    }
}

```

------

Main启动组织框架(所有控件管理)

```c#
using UnityEngine;

/// <summary>
/// 启动组织框架(所有控件管理)
/// </summary>
public class Main : MonoBehaviour
{
    //可以在Main里实例化单例
    public static Main Instance;

    public UIManager uiManager;

    void Awake()
    {
        // 1. 可以找到 uiManager
        uiManager = gameObject.AddComponent<UIManager>();
        // 2. 同样可以找到 uiManager 调用里面方法
        // uiManager = Main.Instance.uiManager.methods;

        gameObject.AddComponent<WWWManager>();

        gameObject.AddComponent<AudioManager>();

        gameObject.AddComponent<NPCManager>();

        

        //只是实例化一次，跨场景也不销毁
        DontDestroyOnLoad(gameObject);
    }
}

```

------

