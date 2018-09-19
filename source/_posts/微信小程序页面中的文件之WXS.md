---
title: 微信小程序页面中的文件之WXS
date: 2018-06-13 17:41:40
tags:
- 微信小程序
---
今天看微信小程序的源码，无意中看到了一个以.wxs为后缀的文件，因为之前没细看开发文档，所以一头雾水，好奇心迫使我查看相关的文档以及查阅一些大牛留下的问题讲解，所以整理记录一下，以备后续查看使用。

<!--more-->

之前看微信小程序中的页面，无非就是wxml、wxss、json、js。但是今天突然又冒出了一个wxs，翻阅文档后原来是一个小程序的脚本语言(可以说是类似于js脚本)

官方说明

    WXS（WeiXin Script）是小程序的一套脚本语言，结合 WXML，可以构建出页面的结构。

我理解的WXS的主要作用就是来封装一些私有变量、公共模块以及方法。

WXS代码可以编写在wxml文件中的`<wxs>`标签内或者以 `.wxs`为后缀名的文件内

- 首先说下以 `.wxs`为后缀名的文件

例：

	// /pages/comm.wxs
	var msg = "hello kitty"
	var fun = function(text){
		return text;
	}

	module.exports = {
		MSG: msg,
		FUN: fun
	}

上述例子在`comm.wxs`文件里编写了WXS代码。该文件可以在其他的`.wxs`文件里引用，也可以在wxml中引用。

接着说下关于里面的几个关键字：

module对象是每个wxs模块均有的一个内置对象，它有一个属性exports可以把模块里的私有变量或者方法暴露给外界。

`注：在其他.wxs文件引入wxs是用关键词require，这儿引入的路径必须是相对路径。`

- 接着是<wxs>标签

例：(连接上下文)
	
	<!--wxml-->
	<wxs module="comm" src="../pages/comm.wxs" />

	<view>{{comm.MSG}}</view>		//页面输出hello kitty

wxs标签有两个属性，一个是module，一个是src；`module`是声明模块，`src`是引用wxs的文件模块。

`注：src只能引用.wxs文件模块，并且必须是相对路径`

wxs标签还有一种写法是，直接在双标签内写wxs。

例：

	<wxs module="foo">
		var text = "sk"
		module.exports = {
			TEXT: text
		}
	</wxs>

	<view>{{foo.TEXT}}</view>    //页面输出sk

上面的例子声明了一个名字为foo的模块，然后将变量暴露出来，供本页面使用。

OVER...