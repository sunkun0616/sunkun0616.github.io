---
title: 微信小程序中关于HTML的解析
date: 2018-05-31 22:13:57
tags:
- 微信小程序
---
最近在研究微信小程序接入数据时，遇到了一个小问题，就是**获取到的数据里有html标签**，然后怎么把它转化为微信小程序结构wxml的。在vue中遇到类似的问题，可以直接使用它的内置属性：v-html。但是在微信小程序中就需使用插件：**WxParse** 和 **Towxml**。

<!--more-->

接下来，说一下这两个插件的用法。

## WxParse





- 首先要下载WxParse插件，地址奉上：[WxParse插件](https://github.com/icindy/wxParse) ,然后放在根目录下。

	![](/img/wxParse_path.png)

- 在需要使用该插件的view(.js文件)中引入wxParse模块 
	
	``` var wxParse = require('/wxParse/wxParse.js') ```

- 在需要使用wxss中引入wxParse.wxss

	``` @import"/wxParse/wxParse.wxss"; ```

- 然后进行数据绑定
	
### 以下是官方文档介绍

	var article = '<div>我是一段html</div>';

	/**
		* wxParse.wxParse(bindName,type,data,target,imagePadding)
		* 1.bindName为绑定的数据名(必填)
		* 2.type可以为html或者markdown(必填)
		* 3.data为传入的具体数据(必填)
		* 4.target为Page对象，一般为this(必填)
		* 5.imagePadding为当图片自适应时左右的单一padding(默认为0，可选)
		*
		**/

	var that = this;
	wxParse.wxParse('article','html',article,that,5)

   自己在项目中可以根据上面来套。

- 在内容页(.wxml文件)中引入模板文件

	<import src="/wxParse/wxParse.wxml" />

然后在下面需要用到解析数据的地方引入

	<template is="wxParse" data="{{wxParseData:article.nodes}}" />

最后试一下可以正常渲染获取到的html数据了。


---------------------------------------------

Towxml
--



同wxParse一样，首先下载[Towxml插件](https://github.com/sbfkcel/towxml)放在根目录下。

在小程序的app.js中引入插件
	
	const Towxml = require('/towxml/main');
	App({
		towxml: new Towxml();
	})

在小程序页面引入模板
	
	<import src="/towxml/entry.wxml" />
	<template is="entry" data="{{...article}}" />

在小程序对应的js中请求数据

	const app = getApp();
	Page({
	    data: {
	        //article将用来存储towxml数据
	        article:{}
	    },

	    onLoad: function () {
	        const _ts = this;
	
	        //请求markdown文件，并转换为内容
	        wx.request({
	            url: 'http://xxx/doc.md',
	            header: {
	                'content-type': 'application/x-www-form-urlencoded'
	            },
	            success: (res) => {
	                //将markdown内容转换为towxml数据
	                let data = app.towxml.toJson(res.data,'markdown');
	
	                //设置文档显示主题，默认'light'
	                data.theme = 'dark';
	
	                //设置数据
	                _ts.setData({
	                    article: data
	                });
	            }
	        });
	    }
	})

引入对应的wxss
	
	/**基础风格样式**/
	@import '/towxml/style/main.wxss';
	
	
	/**如果页面有动态主题切换，则需要将使用到的样式全部引入**/
	
	/**主题配色（浅色样式）**/
	@import '/towxml/style/theme/light.wxss';
	
	/**主题配色（深色样式）**/
	@import '/towxml/style/theme/dark.wxss';

然后试一下获取到的html标签数据，可以完美的渲染为wxml结构。