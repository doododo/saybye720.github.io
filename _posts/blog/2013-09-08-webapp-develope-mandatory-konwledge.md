---
layout:		post
title:		移动端webapp开发必备知识
category:	blog
description: 移动设备的用户越来越多，每天android手机的激活量都已经超过130万台，所以我们面向移动终端的WebAPP
---

###基本概念
####CSS pixels与device pixels
CSS pixels: 浏览器使用的抽象单位， 主要用来在网页上绘制内容。   
device pixels: 显示屏幕的的最小物理单位，每个dp包含自己的颜色、亮度。   
等值的 CSS :  `pixels`在手机屏幕上占多大的位置，这不是固定的，这取决于很多属性。经过分析和总结，我们可以得出这么一条公式: 1 `CSS pixels` = （`devicePixelRatio`）^2 device pixels （`^2是平方的意思，至于 devicePixelRatio是什么东西，后面会讲解`）
####PPI
PPI，有时也叫DPI，所表示的是每英寸所拥有的像素（pixel）数目，数值越高，即代表显示屏能够以越高的密度显示图像。（`注：这里的像素，指的是device pixels`）搞清楚了PPI是什么意思，我们就能很容易理解PPI的计算方式了，我们需要首先算出手机屏幕的对角线等效像素，然后处以对角线（`我们平常所说的手机屏幕尺寸就是说的手机屏幕对角线的长度`），就可以得到PPI了。准确的计算公示大家可以参照下图。比较有意思的是，根据公式计算出来的iPhone 4的PPI为330，要比苹果官方公布的326要高一点点。
