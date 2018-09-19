---
title: Vue中页面跳转后的组件缓存
date: 2018-09-04 11:05:46
tags:
- Vue.js
---
在vue的组件中，一般会遇到list跳转到detail，然后点击返回重新回到list。但是由于vue-router页面跳转后，之前的页面会销毁，所以当返回时，页面会重新渲染dom。

<!--more-->

上面说的那种情况并不是不行，只是每次点击返回都会重新加载影响用户体验，所以这个时候就要用到`keep-alive`来缓存需要缓存的组件。

如果整个站都需要的缓存的话，就直接在App.vue的文件里的`router-view`外层添加一个`keep-alive`标签。但是有的时候是根据需求来缓存路由，这个时候就要用到v-if判断。首先需要在router里添加属性来指定该组件需不需要缓存，可以放在meta下，如keepAlive: true然后在App.vue里判断，如：

> 判断需要缓存的组件

	<keep-alive><router-view v-if=”$route.meta.keepAlive”></router-view></keep-alive>

> 判断不需要缓存的组件

	<keep-alive><router-view v-if=”!$route.meta.keepAlive”></router-view></keep-alive>