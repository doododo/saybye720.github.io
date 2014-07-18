---
layout:		post
title:		移动端webapp开发必备知识
category:	blog
description: 移动设备的用户越来越多，每天android手机的激活量都已经超过130万台，所以我们面向移动终端的WebAPP
---

## 基本概念
### CSS pixels与device pixels
CSS pixels: 浏览器使用的抽象单位， 主要用来在网页上绘制内容。   
device pixels: 显示屏幕的的最小物理单位，每个dp包含自己的颜色、亮度。   
等值的 CSS :  `pixels`在手机屏幕上占多大的位置，这不是固定的，这取决于很多属性。经过分析和总结，我们可以得出这么一条公式: 1 CSS pixels = （`devicePixelRatio`）\^2 device pixels （`\^2是平方的意思，至于 devicePixelRatio是什么东西，后面会讲解`）
### PPI
PPI，有时也叫DPI，所表示的是每英寸所拥有的像素（pixel）数目，数值越高，即代表显示屏能够以越高的密度显示图像。（`注：这里的像素，指的是device pixels`）搞清楚了PPI是什么意思，我们就能很容易理解PPI的计算方式了，我们需要首先算出手机屏幕的对角线等效像素，然后处以对角线（`我们平常所说的手机屏幕尺寸就是说的手机屏幕对角线的长度`），就可以得到PPI了。准确的计算公示大家可以参照下图。比较有意思的是，根据公式计算出来的iPhone 4的PPI为330，要比苹果官方公布的326要高一点点。   
![Auto Edit Bar](http://note.youdao.com/yws/res/3308/EBF9F2F0FDAB4F0DB0D97CA3E38F1565)
同理，以HTC G7为例，480*800的分辨率，3.7英寸，算出来就是252的PPI。   
### 密度决定比例
我们计算PPI就是为了知道一部手机设备是属于哪个密度区间的，因为不同的密度区间，对应着不同的默认缩放比例，这是一个很重要的概念。
![Auto Edit Bar](http://note.youdao.com/yws/res/3310/92E76A2AAC14402EBEE567952CF99E8A)   
由上图可知，PPI在120-160之间的手机被归为低密度手机，160-240被归为中密度，240-320被归为高密度，320以上被归为超高密度（`Apple给了它一个上流的名字——retina`）。   
这些密度对应着一个特定的缩放比例值，拿我们最熟悉的iphone4或4s来说，它们的PPI是326，属于超高密度的手机。当我们书写一个宽度为320px的页面放到iphone中显示，你会发现，它竟然是满宽的。这是因为，页面被默认放大了两倍，也就是640px，而iphone4或4s的宽，正是640px。   
图中把高密度的一类圈起来，是因为这是android手机的统计数据，在国内安卓手机市场中，高密度的设备占了绝大多数的市场份额，这是很重要的一点，也是我们做安卓端webapp要注意的关键点。   
### viewport的使用
viewport总共有5个属性，分别如下:

     <meta name="viewport" content=" height = [ pixel_value |device-height] , width = [ pixel_value |device-width ] , initial-scale = float_value , minimum-scale = float_value , maximum-scale = float_value , user-scalable =[yes | no] , target- densitydpi = [ dpi_value | device-dpi| high-dpi | medium-dpi | low-dpi] " />

在这些属性里面，我们重点关注target-densitydpi，这个属性可以改变设备的默认缩放。medium-dpi是target-densitydpi的默认值，如果我们显式定义`target-densitydpi=device-dpi`，那么设备就会按照真实的dpi来渲染页面。打个比方说，一张320*480的图片，放在iphone4里面，默认是占满屏幕的，但如果定义了`target-densitydpi=device-dpi`，那么图片只占屏幕的四分之一（`二分之一的平方`），因为iphone4的分辨率是640*960。

## 解决方案
### 简单粗暴
如果我们按照320px宽的设计稿去制作页面，并且不做任何的设置，页面会默认自动缩放到跟手机屏幕相等的宽度（`这是由于 medium-dpi是target-densitydpi的默认值，和不同密度对应不同缩放比例所决定的，这一切都是移动设备自动完成的`）。所以这种解决方案，简单，粗暴，有效。但有一个致命的缺点，对于高密度和超高密度的手机设备，页面（`特别是图片`）会失真，而且密度越多，失真越厉害。
### 极致完美
在这种方案中，我们采用 `target-densitydpi=device-dpi`，这样一来，手机设备就会按照真实的像素数目来渲染，用专业的话来说，就是1 CSS `pixels = 1 device pixels`。比如对于 640*960的 iphone，我们就可以做出 640*960的页面，在iphone上显示也不会有滚动条。当然，对于其他设备，也需制作不同尺寸的页面，所以这种方案往往是使用媒体查询来做成响应式的页面。这种方案可以在特定的分辨率下完美呈现，但是随着要兼容的不同分辨率越多，成本就越高，因为需要为每一种分辨率书写单独的代码。下面举个简单的例子:

     <meta name="viewport"content="target-densitydpi=device-dpi, width=device-width " />


     #header { background:url (medium-density-image.png); } @media screen and (- webkit -device-pixel-ratio:1.5) { /* CSS for high-density screens */ #header { background:url (high-density-image.png);} } @media screen and (- webkit -device-pixel-ratio:0.75) { /* CSS for low-density screens */ #header { background:url (low-density-image.png);} }

![Auto Edit Bar](http://note.youdao.com/yws/res/3309/191E55A9473C41E59B0BAB580FAE2783)

### 合理折中
针对安卓设备绝大多数是高密度，部分是中密度的特点，我们可以采用一个折中的方案：我们对480px宽的设计稿进行还原，但是页面制却做成320px宽（`使用background-size来对图片进行缩小`），然后，让页面自动按照比例缩放。这样一来，低密度的手机有滚动条（`这种手机基本上已经没有人在用了`），中密度的手机会浪费一点点流量，高密度的手机完美呈现，超高密度的手机轻微失真（超高密度的安卓手机很少）。这种方案的优点非常明显：只需要一套设计稿，一套代码（`这里只是讨论安卓手机的情况`）。
## 开发调试
### weinre远程实时调试
Web开发者经常使用Firefox的firebug或者Chrome的开发人员工具进行Web调试，包括针对JavaScript，DOM元素和CSS样式的调试。但是，当我们期望为移动Web站点或应用进行调试时，这些工具就很难派上用场。

[weinre][1]就是一个帮助我们在桌面来远程调试运行在移动设备浏览器内的Web页面或应用的调试工具。weinre是WEb INspector REmote的简写，现在是Apache的一个开源项目，托管在[github][2]。
下面将介绍如此在日常工作使用它。
首先，我们要下载weinre的jar包——项目官方已经找不到该jar文件，网上能够找到，这里建议搭建个独立的web服务器，jar运行后是一个本地的服务器，和web服务器差不多~~
然后通过运行dos命令来启动它（`请注意在你的电脑上已经安装有JDK`）。运行命令如下，需要把路径改成你的实际文件位置：

    java -jar d:toolsweinre-jarweinre.jar –httpPort 8081 –boundHost -all- 
    ##（httpPort是指定服务端口，boundHost参数说明可以使用IP访问，all参数代表支持所有的host）。

![CMD](http://note.youdao.com/yws/res/3311/4770DE4281894B94A4E058A961485B3C)

访问localhost:8081，如果看到如下的页面，说明weinre已经启动成功：
![Success](http://note.youdao.com/yws/res/3313/40FF5A9137804081A2EB291CED5E4B4F)

输入debug client user interface地址（调试客户端UI地址）。本例中即：`http://localhost:8081/client/#anonymous`，其中#anonymous是默认的调试id（debug id）。如果这个weinre调试服务器只是由你一个人使用，那么你可以使用默认的`debug id：anonymous`。 启动的weinre调试客户端ui如下图：

![UI](http://note.youdao.com/yws/res/3312/A757FB2D12604C76921BC32D6F9C49B7)
在需要调试的页面加入中以下脚本：

     <script type=”text/javascript” src=”http://localhost:8081/target/target-script-min.js#anonymous”></script>

注意把localhost换成手机能够访问的真实IP地址。当手机访问这个页面时，weinre客户端就会检测到目标设备，然后就可以对它进行调试了
![Debug](http://note.youdao.com/yws/res/3314/6FB3A1BFABD74BFF84478A19EFAF8906)
因为手机上不方便截图，我这里就用两个浏览器窗口来展示效果，其实手机上的效果跟右边是一样的。

### AVD模拟器调试
静态页面并不能满足我们的需求，很多实际效果比如touch事件，滚动事件，键盘输入事件等，都需要在真实的环境下测试，这时就需要用到模拟器。就像我们测试ie6一样，AVD模拟器可以类比于PC上的虚拟机，当我们需要测试某一特定的机型时，我们可以新建一个AVD，进行一系列的测试。不过使用AVD的前提是已经部署好android的开发环境，这个需要JDK + android SDK + Eclipse + ADT，还是稍微有点繁琐。

### 手机抓包与配host
在PC上，我们可以很方便地配host，但是手机上如何配host，这是一个问题。
这里主要使用fiddler和远程代理，实现手机配host的操作，具体操作如下：
<ul>
	<li>1 首先，保证PC和移动设备在同一个局域网下；</li>
	<li>2 PC上开启fiddler，并在设置中勾选`allow remote computers to connect`</li>
</ul>

![Fiddler Options](http://note.youdao.com/yws/res/3316/F8C4972716B547ABA0C07BE587E9E8FE)

<ul>
	<li>3 手机上设置代理，代理IP为PC的IP地址，端口为8888（`这是fiddler的默认端口`）。通常手机上可以直接设置代理，如果没有，可以去下载一个叫ProxyDroid的APP来实现代理的设置。</li>
	<li>4 此时你会发现，用手机上网，走的其实是PC上的fiddler，所有的请求包都会在fiddler中列出来，配合willow使用，即可实现配host，甚至是反向代理的操作</li>
</ul>

## 总结

以上就是我们在实际开发中积累的一些经验和技巧，希望能够给大家一些帮助，如果你有好的方法或者工具，也请在留言中分享~~

[1]: http://people.apache.org/~pmuellr/weinre/docs/latest/Home.html
[2]: https://github.com/apache/incubator-cordova-weinre