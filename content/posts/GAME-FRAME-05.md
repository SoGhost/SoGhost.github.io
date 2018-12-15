---
title: UI模块封装——游戏框架-05
date: 2018-08-31T17:52:54+08:00
lastmod: 2018-08-31T17:52:54+08:00
cover: "/images/default1.png"
draft: false
categories: ["GAME-FRAME"]
tags: ["RPG"]
description: UI模块封装与MVC架构、制作测试登陆注册界面
---

# UI模块

> ## UI 流程：
>
>  - <font color=#00000>**拼出大致UI效果图**
>  - UI小图片 **--->** TexturePacker**打包图集** **（.png有A通道，.jpg没有）**  **--->**  Unity下载TexturePacker的导入插件
>  - **拼UI：** 新建Canves **--->**  *新建Panel* （背景设为UI效果图，照着拼，以Panel为单位，一个Panel一个界面） **--->** *划分UI层级*
>  - **写代码：** **↓↓↓↓**</font>

------


|                                                     | **<font color=#00000>MVC模式**（Model–view–controller）</font> |
| :-------------------------------------------------: | :----------------------------------------------------------- |
|    **<font color=#DB7093>模型（Model）</font>**     | <font color=#DB7093>数据模型（数据结构，username、password）。用于封装与应用程序的业务逻辑相关的数据以及对数据的处理方法。</font> |
|     **<font color=#9400D3>视图（View）</font>**     | <font color=#9400D3>能够实现数据有目的的显示（理论上，这不是必需的）。</font> |
| **<font color=#4169E1>控制器（Controller）</font>** | <font color=#4169E1>逻辑控制层。起到不同层面间的组织作用，用于控制应用程序的流程。它**处理事件并作出响应**。**“事件”包括用户的行为和数据 Model 上的改变**。</font> |

>    **控制器（Controller）：**由**UIManager**管理所有Panel，由**PanelManager**管理所有子控件。
>
>    1. 子控件发生事件让其主动’上报‘，所以给需要的控件添加UIBehaviour脚本。
>    2. UIBehaviour会将自己注册到UIManager。
>    3. **UIManager：**保存注册到UIManager里的子控件。
>

>    ------
>

>    **销毁：**
>
>    1. 将后面还<u>用到</u>的界面**隐藏**（背包界面，菜单界面）
>    2. 将后面<u>用不到</u>的界面**Destory**（登录界面）
>

>    ------
>

>    **资源的加载：**
>
>    1. Loading界面 还会加载后面要用的资源到内存（Resources.Load），不实例化。用到时GameObject.Instantiate将内存中资源实例化到场景中。
>    2. 进入二级Loading界面会将前面的资源卸载，加载后面的资源。

------


## 注意事项：

>    - **在同一个Panel里要使用的控件的名字不能一样！**
>
>    - **Panel名字也不能一样**，不同Panel的子控件可以同名。
>
>    - 解决子控件下面的子控件名字重复
>
>      > UISubManager（二级管理）：
>      >
>      > 1. 由此情况的控件绑定UISubManager，并注册到UIManager
>      >
>      > 2. 管理子控件 Dictionary<string, GameObject>
>      >
>      > 3. UIManager管理UISubManager
>

------


## UI MVC 架构演示

>```c#
>using System.Collections.Generic;
>using UnityEngine;
>using UnityEngine.UI;
>using UnityEngine.Events;
>using UnityEngine.EventSystems;//事件类型
>
>/// <summary>
>/// UIManager --- UI管理器
>/// </summary>
>public class UIManager : MonoBehaviour
>{
>    public static UIManager instance;
>
>    /// <summary>
>    /// 1. allWidget 所有控件
>    /// 2. Manager管理多个Panel，Panel又管理多个控件（二维树形结构），Panel是个容器。
>    /// 3. 第一个string表示Panel的名字，第二个string表示控件的名字。
>    /// </summary>
>    Dictionary<string, Dictionary<string, GameObject>> allWidget;
>
>    //注册控件
>    public void RegistGameObject(string panelName, string widgetName, GameObject obj)
>    {
>        // 1. 判断allWidget 里面是否包含 Dictionary<string, GameObject>
>        // 2. 不包含则Dictionary<string, GameObject> 没有实例化，则进行实例化
>        if (allWidget.ContainsKey(panelName))
>        {
>            allWidget[panelName] = new Dictionary<string, GameObject>();
>        }
>
>        allWidget[panelName].Add(widgetName, obj);
>    }
>
>    void Awake()
>    {
>        instance = this;
>
>        allWidget = new Dictionary<string, Dictionary<string, GameObject>>();
>    }
>
>    /// <summary>
>    /// 获取某一个Panel下的子控件
>    /// </summary>
>    /// <param name="panelName"></param>
>    /// <param name="widgetName"></param>
>    /// <returns></returns>
>    public GameObject GetGameObject(string panelName, string widgetName)
>    {
>        if (allWidget.ContainsKey(panelName))
>        {
>            return allWidget[panelName][widgetName];
>        }
>
>        return null;
>    }
>    
>    //给二级子控件添加按钮监听
>    public void AddButtonListener(string widgetName, UnityAction action)
>    {
>        //根据名字找到子控件的transform
>        Transform tmpTransform = GetChildTransform(widgetName);
>        ;
>        Button tmpBtn = tmpTransform.GetComponent<Button>();
>
>        if (tmpBtn != null)
>        {
>            tmpBtn.onClick.AddListener(action);
>        }
>    }
>    
>	/// <summary>
>	/// 销毁 Dictionary 不用的
>	/// </summary>
>	#region Destory
>
>	//只能删除一个子控件
>	public void UnRegistGameObject(string panelName, string widgetName)
>	{
>    	//将子控件从allWidget删除
>    	if (allWidget.ContainsKey(panelName))
>    	{
>        	if (allWidget[panelName].ContainsKey(widgetName))
>            	allWidget[panelName].Remove(widgetName);
>    	}
>	}
>
>	//删除Panel（删除所有子控件）
>	public void UnRegistPanel(string panelName)
>	{
>    	if (allWidget.ContainsKey(panelName))
>    	{
>        	allWidget[panelName].Clear();
>        	allWidget[panelName] = null;
>    	}
>	}
>	#endregion
>}
>
>/// <summary>
>/// UIBase：
>/// 1. MVC C层（Controller）
>/// 2. C层共同的行为
>/// </summary>
>public class UIBase : MonoBehaviour
>{
>    void Awake()
>    {
>        //寻找所有子物体的Transform（RectTransform属于Transform子类，亦可被搜到）
>        Transform[] allChildren = transform.GetComponentsInChildren<Transform>();
>
>        for (int i = 0; i < allChildren.Length; i++)
>        {
>            // 分析物体名字的开头是否有特殊开头
>            // 1. allChildren[i].name.StartsWith("_N") 物体检测名字开头是否有_N
>            // 2. allChildren[i].name.EndsWith("_N") 物体检测名字结尾是否有_N
>            if (allChildren[i].name.StartsWith("_N"))
>            {
>                //给有特殊开头的物体，添加 UIBehaviour 脚本
>                allChildren[i].gameObject.AddComponent<UIBehaviour>();
>            }
>            
>            //判断一级控件是否有'_C'开头
>            if (allChildren[i].name.StartsWith("_C"))
>            {
>                //给有特殊开头的物体，添加 UISubBehaviour 脚本
>			allChildren[i].gameObject.AddComponent<UISubBehaviour>();
>            }
>        }
>    }
>    
>    // 给二级子控件添加按钮监听：
>    // 1. widgetName：一级子控件的名字
>    // 2. secondaryWidgetName：二级子控件的名字
>    // 3. AddSubButtonListener改为AddButtonListener同名API自动重载，减少API数量，优化代码
>    public void AddButtonListener(string widgetName, string secondaryWidgetName, UnityAction action)
>    {
>        UISubManager tmpSub = GetSubManager(widgetName);
>
>        if (tmpSub != null)
>        {
>            tmpSub.AddButtonListener(widgetName, action);
>        }
>    }
>
>    //获得SubManager
>    public UISubManager GetSubManager(string widgetName)
>    {
>        //如果注册了，在UIManager能找到
>        GameObject tmpObj = GetWidget(widgetName);
>
>        if (tmpObj != null)
>        {
>            return tmpObj.GetComponent<UISubManager>();
>        }
>
>        return null;
>    }
>
>	//获得控件物体
>    public GameObject GetWidget(string widgetName)
>    {
>        return  UIManager.instance.GetGameObject(transform.name, widgetName);
>    }
>
>	//获得控件上的UIBehaviour
>    public UIBehaviour GetBehaviour(string widgetName)
>    {
>        GameObject tmpObj = GetWedagte(widgetName);
>
>        if (tmpObj != null)
>        {
>            return tmpObj.GetComponent<UIBehaviour>();
>        }
>
>        return null;
>    }
>
>    //添加Button事件
>    public void AddButtonListener(string widgetName, UnityAction action)
>    {
>        UIBehaviour tmpBehaviour = GetBehaviour(widgetName);
>
>        if (tmpBehaviour != null)
>        {
>            tmpBehaviour.AddButtonlistener(action);
>        }
>    }
>
>    //改变Text显示的文字
>    public void ChangeTextContent(string wigageName, string content)
>    {
>        UIBehaviour tmpBehaviour = GetBehaviour(widgetName);
>
>        if (tmpBehaviour != null)
>        {
>            tmpBehaviour.ChangeTextContent(content);
>        }
>    }
>    
>    //销毁Panel
>    void OnDestroy()
>    {
>        UIManager.instance.UnRegistPanel(transform.name);
>    }
>    
>    //添加拖拽事件
>    public void AddDragHandler(string wigageName, UnityAction<BaseEventData> action)
>    {
>        UIBehaviour tmpBehaviour = GetBehaviour(wigageName);
>
>        if (tmpBehaviour != null)
>        {
>            tmpBehaviour.AddDragHandler(action);
>        }
>    }
>}
>
>/// <summary>
>/// 控件上的 UIBehaviour
>/// </summary>
>public class UIBehaviour : MonoBehaviour
>{
>    void Awake()
>    {
>        //获取Panel名字
>         UIBase tmpBase = transform.GetComponentInParent<UIBase>();
>
>        //tmpBase.name可以直接获取Panel物体的名字、transform.name控件名字、gameObject表示自己
>        UIManager.instance.RegistGameObject(tmpBase.name, transform.name, gameObject);
>    }
>
>    /// <summary>
>    /// 给按钮添加事件（按下按钮时）
>    /// </summary>
>    /// <param name="action"></param>
>    public void AddButtonlistener(UnityAction action)
>    {
>        Button tmpButton = transform.GetComponent<Button>();
>
>        if (tmpButton != null)
>        {
>            tmpButton.onClick.AddListener(action);
>        }
>    }
>
>    /// <summary>
>    /// 给Slider添加事件（当滑动条改变值时）
>    /// </summary>
>    /// <param name="action"></param>
>    public void  AddSliderListener(UnityAction<float> action)
>    {
>        Slider tmpSlider = transform.GetComponent<Slider>();
>
>        if (tmpSlider != null)
>        {
>            tmpSlider.onValueChanged.AddListener(action);
>        }
>    }
>
>    /// <summary>
>    /// 给输入框添加事件（当结束编辑时）
>    /// </summary>
>    /// <param name="action"></param>
>    public void AddInputFiledEndEdtiorListener(UnityAction<string> action)
>    {
>        InputField tmpInput = transform.GetComponent<InputField>();
>
>        if (tmpInput != null)
>        {
>            tmpInput.onEndEdit.AddListener(action);
>        }
>    }
>
>    /// <summary>
>    /// 给输入框添加事件（每次编辑完成时）
>    /// </summary>
>    /// <param name="action"></param>
>    public void AddInputFiledValueChangedListener(UnityAction<string> action)
>    {
>        InputField tmpInput = transform.GetComponent<InputField>();
>
>        if (tmpInput != null)
>        {
>            tmpInput.onValueChanged.AddListener(action);
>        }
>    }
>
>    /// <summary>
>    /// 修改文字
>    /// </summary>
>    /// <param name="content"></param>
>    public void ChangeTextContent(string content)
>    {
>        Text tmpText = transform.GetComponent<Text>();
>
>        if (tmpText != null)
>        {
>            tmpText.text = content;
>        }
>    }
>
>    /// <summary>
>    /// 修改图片
>    /// </summary>
>    /// <param name="spriteName"></param>
>    public void ChangeImage(Sprite spriteName)
>    {
>        Image tmpImage = transform.GetComponent<Image>();
>
>        if (tmpImage != null)
>        {
>            tmpImage.sprite = spriteName;
>        }
>    }
>    
>    /// <summary>
>    /// 创建接口-->添加拖拽处理事件
>    /// </summary>
>    /// <param name="action"></param>
>    public void AddDragHandler(UnityAction<BaseEventData> action)
>    {
>        //获取事件系统
>        EventTrigger trigger = gameObject.GetComponent<EventTrigger>();
>
>        //如果Event事件不存在 动态添加
>        if (trigger == null)
>            trigger = gameObject.AddComponent<EventTrigger>();
>
>        //事件实体（到底是哪个事件）
>        EventTrigger.Entry entry = new EventTrigger.Entry();
>
>        //事件类型
>        // 1. Drag 拖拽
>        entry.eventID = EventTriggerType.Drag;
>
>        //事件回调
>        entry.callback = new EventTrigger.TriggerEvent();
>
>        //添加回调函数
>        entry.callback.AddListener(action);
>
>        //监听事件
>        trigger.triggers.Add(entry);
>    }
>
>    /// <summary>
>    /// 开始拖拽事件
>    /// </summary>
>    /// <param name="action"></param>
>    public void AddOnBeginDrag(UnityAction<BaseEventData> action)
>    {
>        EventTrigger trigger = gameObject.GetComponent<EventTrigger>();
>
>        if (trigger == null)
>            trigger = gameObject.AddComponent<EventTrigger>();
>
>        EventTrigger.Entry entry = new EventTrigger.Entry();
>
>        entry.eventID = EventTriggerType.BeginDrag;
>
>        entry.callback = new EventTrigger.TriggerEvent();
>
>        entry.callback.AddListener(action);
>        
>        trigger.triggers.Add(entry);
>    }
>
>    /// <summary>
>    /// 单击事件
>    /// </summary>
>    /// <param name="action"></param>
>    public void AddPointerClick(UnityAction<BaseEventData> action)
>    {
>        EventTrigger trigger = gameObject.GetComponent<EventTrigger>();
>
>        if (trigger == null)
>            trigger = gameObject.AddComponent<EventTrigger>();
>
>        EventTrigger.Entry entry = new EventTrigger.Entry();
>
>        entry.eventID = EventTriggerType.PointerClick;
>
>        entry.callback = new EventTrigger.TriggerEvent();
>
>        entry.callback.AddListener(action);
>
>        trigger.triggers.Add(entry);
>    }
>}
>
>/************************ Loading ********************************/
>
>/// <summary>
>/// Loading场景控制器
>/// </summary>
>public class LoadingCtrl : UIBase
>{
>    /// <summary>
>    /// 给Loading场景的LoadingButton动态绑定此脚本
>    /// </summary>
>    void Start()
>    {
>        AddButtonListener("_NLoadingButton", OnClick);
>
>        ChangeTextContent("_NText", "更改文字成功!!");
>        
>        AddDragHandler("_NImage", DragHandler);
>        
>        AddButtonListener("_CWidget-1", "_NCSecondWidget", OnClick);
>        AddButtonListener("_CWidget-2", "_NCSecondWidget", OnClick);
>    }
>
>    public void OnClick()
>    {
>        Debug.Log("Button is click!!");
>    }
>    
>    public void DragHandler(BaseEventData eventData)
>    {
>        PointerEventData pointerEventData = (PointerEventData)eventData;
>
>        transform.position = pointerEventData.position;
>    }
>}
>
>public class LoadingModel{}
>
>public class LoadingLogic{}
>
>/************************** Regist *****************************/
>
>/// <summary>
>/// Regist控制器
>/// Controller 逻辑控制层
>/// </summary>
>public class RegistCtrl : UIBase
>{
>    RegistModel RegistModel;
>    RegistLogic registLogic;
>
>    void Start()
>    {
>        RegistModel = new RegistModel();
>        registLogic = new RegistLogic();
>
>        AddButtonListener("_NLoadingButton", registLogic.OnClick);
>
>        ChangeTextContent("_NText", "更改文字成功!!");
>    }
>}
>
>/// <summary>
>/// Modle 存储注册用到的数据
>/// </summary>
>public class RegistModel
>{
>    public string password;
>    public string userName;
>}
>
>/// <summary>
>/// 逻辑层
>/// </summary>
>public class RegistLogic
>{
>    public void OnClick()
>    {
>        Debug.Log("Regist is click!!");
>    }
>}
>
>/********************************************************/
>
>/// <summary>
>/// UISubManager绑定在管理二级子控件的控件上
>/// </summary>
>public class UISubManager : MonoBehaviour
>{
>    Dictionary<string , Transform> allChild;
>
>    void Awake()
>    {
>        UIBase tmpBase = GetComponentInParent<UIBase>();
>
>        UIManager.instance.RegistGameObject(tmpBase.name, transform.name, gameObject);
>
>        allChild = new Dictionary<string, Transform>();
>
>        Transform[] tmpChild = transform.GetComponentsInChildren<Transform>();
>
>        //所有子控件存到allChild里
>        for (int i = 0; i < tmpChild.Length; i++)
>        {
>        	//如果二级子控件前缀有’_NC‘则存入allChild里
>            if (tmpChild[i].name.StartsWith("_NC"))
>                allChild.Add(tmpChild[i].name, tmpChild[i]);
>        }
>    }
>
>    //根据名字获取ChildTransform
>    public Transform GetChildTransform(string widgetName)
>    {
>        return allChild[widgetName];//只有一层，所以不存在就返回null
>    }
>
>    //提供销毁借口
>    void OnDestroy()
>    {
>        if (allChild != null)
>        {
>            allChild.Clear();
>            allChild = null;
>        }
>    }
>}
>```
>
>

------


## UI 实例

>**<font color=#8FBC8F>制作登录注册界面</font>**
>
>1. 获取用户名和密码的输入（给控件加上特殊名字标记表示使用）
>2. 
>
>------
>
>
>
>```c#
>using System.Collections.Generic;
>using UnityEngine;
>using UnityEngine.Events;
>using UnityEngine.UI;
>
>public class LoginCtr : UIBase {
>
>    LoginModel loginModel;
>    LoginLogic loginLogic;
>
>    void Start () {
>        loginModel = new LoginModel();
>        loginLogic = new LoginLogic(loginModel, this);
>
>        BindEvent();
>    }
>
>    // 事件绑定
>    public void BindEvent()
>    {
>        AddInputFiledListener("UserName_N", new UnityAction<string>(loginLogic.GetUserName));
>        AddInputFiledListener("Password_N", new UnityAction<string>(loginLogic.GetPassword));
>
>        AddButtonListener("Login_Btn_N", new UnityAction(loginLogic.LoginClick));
>        AddButtonListener("Regist_Btn_N", new UnityAction(loginLogic.RegistClick));
>    }
>
>    public void HideSelf()
>    {
>        GameObject root = GetWidget("Root_N");
>
>        root.SetActive(false);
>    }
>}
>
>public class LoginModel
>{
>    // 封装性 避免修改
>    public string UserName { get; set; }
>    public string Password { get; set; }
>}
>
>public class LoginLogic
>{
>    LoginModel loginModel;
>    LoginCtr loginCtr;
>
>    public LoginLogic(LoginModel model, LoginCtr ctr)
>    {
>        loginModel = model;
>        loginCtr = ctr;
>    }
>
>    public void GetUserName(string userName)
>    {
>        loginModel.UserName = userName;
>    }
>
>    public void GetPassword(string password)
>    {
>        loginModel.Password = password;
>    }
>
>    public void LoginClick()
>    {
>        // 1. 从网络请求 检测用户名和密码是否正确
>        // 2. 从本地数据库查询
>
>        Debug.Log("Login is click!!");
>    }
>
>    /// <summary>
>    /// 通用的代码 都可以写到 UIBase 父类里面
>    /// </summary>
>    public void RegistClick()
>    {
>        //弹出注册界面
>        //加载Regist的Prefab到内存，还没有实例化
>        Object obj = Resources.Load("UI/Regist/Regist");
>
>        //实例化 Panel
>        GameObject registPanel = Object.Instantiate(obj) as GameObject;
>
>        //去掉实例化多余Clone名字
>        registPanel.name = registPanel.name.Replace("(Clone)", "");
>        
>        Transform mainCanvas = loginCtr.GetCanvas();
>
>        //设置父类
>        registPanel.transform.SetParent(mainCanvas, false);
>
>        //给注册界面添加控制层
>        registPanel.AddComponent<RegistCtr>();
>
>        //隐藏登录界面
>        loginCtr.HideSelf();
>    }
>}
>
>/********************* UIBase ************************/
>
>public class UIBase : MonoBehaviour
>{
>    /// <summary>
>    /// 给需要用到的控件动态添加Behaviour脚本
>    /// </summary>
>    void Awake()
>    {
>    	//获得挂载脚本物体下所有控件
>        Transform[] allChildren = transform.GetComponentsInChildren<Transform>();
>
>        for (int i = 0; i < allChildren.Length; i++)
>        {
>            if (allChildren[i].name.EndsWith("_N"))
>            {
>                allChildren[i].gameObject.AddComponent<UIBehaviour>();
>            }
>        }
>    }
>
>    public GameObject GetWidget(string widgetName)
>    {
>        // transform.name是GetWidget()被GetBehaviour()被挂在UIBehaviour上的控件所调用出来的名字
>        // transform.name就是挂有UIBehaviour控件的名字
>        return UIManager.instance.GetGameObject(transform.name, widgetName);
>    }
>
>    public UIBehaviour GetBehaviour(string widgetName)
>    {
>        GameObject obj = GetWidget(widgetName);
>
>        if (obj != null)
>        {
>            return obj.GetComponent<UIBehaviour>();
>        }
>
>        return null;
>    }
>
>    public void AddButtonListener(string widgetName, UnityAction action)
>    {
>        UIBehaviour tmpBehaviour = GetBehaviour(widgetName);
>
>        if (tmpBehaviour != null)
>        {
>            tmpBehaviour.AddButtonListener(action);
>        }
>    }
>
>    public void AddInputFiledListener(string widgetName, UnityAction<string> action)
>    {
>        UIBehaviour tmpBehaviour = GetBehaviour(widgetName);
>
>        if (tmpBehaviour != null)
>        {
>            tmpBehaviour.AddInputFiledEndEditorListener(action);
>        }
>    }
>
>    #region 获取Canvas
>    public Transform GetCanvas()
>    {
>        return UIManager.instance.MainCanvas;
>    }
>    #endregion
>}
>
>/********************* UIBehaviour ************************/
>
>/// <summary>
>/// UIBehaviour主要作用：
>/// 1. 获取控件父物体名字
>/// 2. 创建接口-->添加事件
>/// </summary>
>public class UIBehaviour : MonoBehaviour
>{
>    void Awake()
>    {
>        //获取每个子控件父物体上的UIBase
>        UIBase tmpBase = transform.GetComponentInParent<UIBase>();
>
>        UIManager.instance.RegistGameObject(tmpBase.name, transform.name, gameObject);
>    }
>
>    //添加按钮点击事件
>    public void AddButtonListener(UnityAction action)
>    {
>        Button tmpButton = transform.GetComponent<Button>();
>
>        if (tmpButton != null)
>        {
>            tmpButton.onClick.AddListener(action);
>        }
>    }
>
>    //添加输入框文字编辑结束事件
>    public void AddInputFiledEndEditorListener(UnityAction<string> action)
>    {
>        InputField tmpInput = transform.GetComponent<InputField>();
>
>        if (tmpInput != null)
>        {
>            tmpInput.onEndEdit.AddListener(action);
>        }
>    }
>}
>
>/********************* UIManager ************************/
>
>public class UIManager : MonoBehaviour
>{
>    public static UIManager instance;
>
>    //将Canvas存在UIManager中，只查找一次
>    public Transform mainCanvas;
>
>    public Transform MainCanvas
>    {
>        get
>        {
>            return mainCanvas;
>        }
>    }
>
>    // allWidget存储用到的一级控件名字 及其子控件名字和物体
>    Dictionary<string, Dictionary<string, GameObject>> allWidget;
>
>    void Awake()
>    {
>        instance = this;
>
>        allWidget = new Dictionary<string, Dictionary<string, GameObject>>();
>
>        mainCanvas = GameObject.FindGameObjectWithTag("MainCanvas").transform;
>    }
>
>    //存储用到的控件
>    public void RegistGameObject(string panelName, string widgetName, GameObject obj)
>    {
>        // 1. 判断输入的一级控件名字是否存在，不存在则先对包含的二级Dictionary实例化
>        // 2. 再将其加入到Dictionary中
>        if (allWidget.ContainsKey(panelName))
>        {
>            allWidget[panelName] = new Dictionary<string, GameObject>();
>        }
>
>        allWidget[panelName].Add(widgetName, obj);//加入二级Dictionary中
>    }
>
>    //获得组件
>    public GameObject GetGameObject(string panelName, string widgetName)
>    {
>        //如果在所有控件名字中找到widgetName
>        if (allWidget.ContainsKey(widgetName))
>        {
>            return allWidget[panelName][widgetName];
>        }
>
>        return null;
>    }
>}
>
>/********************* Regist ************************/
>
>public class RegistCtr : UIBase {
>
>	void Start () {
>		
>	}
>
>    public void BindEvent()
>    {
>
>    }
>}
>
>public class RegistModel
>{
>
>}
>
>public class RegistLogic
>{
>    
>}
>```