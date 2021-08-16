---
title: 在Vue中使用NProgress
abbrlink: f007732d
date: 2021-01-26 15:50:54
description: 路由跳转时使用NProgress可在浏览器顶部显示进度条。
cover: /images/vue_cover.png
tags:
	- 前端
	- Vue
categories: JavaScript
top_img: /images/default_cover.jpg
---

## 前言
路由跳转时使用NProgress可在浏览器顶部显示进度条,让用户可知加载进度。

## 安装
```javascript
$ npm install --save nprogress 或者
$ yarn add nprogress

//用法
NProgress.start();
NProgress.done();
```

## 使用
在`main.js`或路由文件`index.js`中增加
```javascript
//导入
import NProgress from 'nprogress'
import 'nprogress/nprogress.css'

router.beforeEach((to, from, next) => {
  NProgress.start()
  next()
})

router.afterEach(() => {
  NProgress.done()
})
```

## 样式修改
在`App.vue`中的`style`中增加
```css
#nprogress .bar {
  background: pink !important; //自定义颜色
  height: 1vh !important; //自定义高度
  ...
}     
```

