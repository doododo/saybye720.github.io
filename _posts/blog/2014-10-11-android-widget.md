---
layout:		post
title:		Android学习一 Android开发过程中常用的控件解析
category:	blog
description:	Android学习基础篇，在Android开发过程中常用的控件解析

---

##TextView ： 文本控件

TextView有一下几个属性

	wrap_content:包裹实际文本内容 
	fill_parent ：当前空间铺满父类容器--2.3api之后添加的一个属性值
	match_parent：当前空间铺满父类容器--2.3api之前添加的一个属性值

##EditView：文本编辑控件
	
	android:hint: 文本框中的默认提示,输入文本后会自动消失
	android:text: 文本框中的文字，输入文本后还存在

将布局文件引入activty当中

setContentView(R.layout.main_activity);   

一般情况下，布局属性与对象的setXXX方法一一对应，比如设置字体颜色，在xml中属性是android:textColor，而在Activity中则是setTextColor方法

设置EditText的android:inputType属性可以限制文本输入类型比如android:inputType="textPassword"为设置输入格式为密码格， android:inputType="phone"为设置输入格式为拨号键盘

在Activity中获取控件对象

(TextView)this.findViewById('R.id.username');

##ImageView :显示图片的控件

	android:src= "@drawable/in_launcher"  --- ImageView的内容图像
	android:background = "@drawable/in_launcher"  ---ImageView的背景图像
	android:background = "#000000"  ---不仅可以引入背景图片，还可以设置ImageView的RGB颜色

##Button 普通按钮  ImageButton 图片按钮

都可以产生一个点击事件

Button有text属性，ImageButton有src属性

访问


