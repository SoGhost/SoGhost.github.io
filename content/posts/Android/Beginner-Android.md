---
title: Beginner
date: 2018-10-26T21:38:20+08:00
lastmod: 2018-10-26T21:38:20+08:00
cover: "/images/default3.png"
draft: false
categories: ["安卓"]
tags: ["Android"]
description: 初学安卓开发安卓
---

# 控件布局

## 1.线性布局(LinearLayout)

> android:orientation="vertical" 线性布局才会用到的布局属性

```xml
<LinearLayout 
    android:id="@+id/11_1" //控件id
    android:layout_height="match_parent" //高度，match_parent匹配父物体位置而自动改变位置
    android:layout_width="200dp" //控件宽度，由dp计量自动调整相对大小
    android:orientation="vertical" //方向
    android:background="#449099" //背景颜色
    android:gravity="Top" //内部元素的对齐方向 Top左上角 center_horizontal水平居中...
    android:padding="20dp" 
  	//本控件对外控件的间距
  	//paddingLeft Right Top Bottom各个方向都可以单独设置间距
  	//父物体方向设置为horizontal, match_parent子控件就会纵向自动排列
  	//layout_margin设置的是控件之间的间距>

<View //View是所有控件的父物体（自动成为其父物体，可以改变子控件与其间距等等）
    android:layout_height="match_parent"
    android:layout_width="match_parent"
    android:background="#FFCC99"/>

<View //让两个控件对半分，设置宽度为0dp，增加属性layout_weight权重为1，子控件就会对半分
    android:layout_width="190dp"
    android:layout_height="match_parent"
    android:layout_weight="1" //权重，按照父控件剩下的位置，所有子控件根据权重大小分配
    android:background="#dd0" />
<View
    android:layout_width="190dp"
    android:layout_height="match_parent"
    android:layout_weight="1" //权重
    android:background="#000000" />
</LinearLayout>
```

------

## 2.相对布局(RelativeLayout)

```xml-dtd
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <View
        android:id="@+id/line1"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:background="#cc00"
        android:layout_alignParentBottom="true" //相对父物体底部对齐
        android:layout_alignParentRight="true" //底部对齐后再右侧对齐，即可达到右下角对齐
        android:layout_toRightOf="@id/SomeWidget" //移动到某个控件的右边
        android:layout_below="@id/SomeWidget" //移动到某个控件的下边/>

</RelativeLayout>
```

# 控件

## TextView

>常用效果:
>
> 	1. 文字大小、颜色
> 	2. 显示不下 使用...
> 	3. 文字 + icon（图文混排）
> 	4. 中划线、下划线
> 	5. 跑马灯

------

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="20dp">
    
    <TextView
        android:id="@+id/text_2"
        android:layout_width="100dp"
        android:layout_height="wrap_content"
        android:maxLines="1" //文字最大行数
        android:ellipsize="end" //当文字超出范围 会在结尾显示...
        android:text="第二蛤蟆你好好么654987!"
        android:drawableRight="@drawable/ic_launcher_background" //图文混排，在文字右侧添加图片
        android:textColor="#000000"
        android:textSize="24sp"
        android:layout_marginTop="10dp"/>

    <TextView
        android:id="@+id/text_4"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" //随文字长度而变大
        android:text="回帖发帖和回帖发帖和回帖发帖和回帖发帖和回帖发帖和回帖发帖和回帖发帖和"
        android:layout_marginTop="10dp" //间距
        android:textColor="#0288D1" //文字颜色
        android:textSize="24sp" //文字大小
        android:singleLine="true" //设置跑马灯 必须设置文字单行
        android:ellipsize="marquee" //文字模式为跑马灯（循环移动播放）
        android:marqueeRepeatLimit="marquee_forever" //循环次数
        android:focusable="true" //启用 是否获取焦点
        android:focusableInTouchMode="true" //启用 触摸模式下可聚焦/>
</LinearLayout>
```

------

