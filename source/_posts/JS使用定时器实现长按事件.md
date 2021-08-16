---
title: JS使用定时器实现长按事件
abbrlink: '81488588'
date: 2020-11-04 09:39:53
description: JS原生事件并没有长按事件,但是我们可以利用一些原生的事件,来实现长按事件。
cover: /images/javascript_cover.jpg
tags:
	- 前端
	- JavaScript
	- Vue
categories: JavaScript
top_img: /images/default_cover.jpg
---
## 前言
JS原生事件并没有长按事件,但是我们可以利用一些原生的事件,来实现长按事件

## 示例
下面的示例是在Vue环境下演示,若使用其他框架转换语法即可。
```HTML
	<div @touchstart="longTap($event)" @touchend="shortTap" @touchmove="contentScroll($event)">定时器实现长按事件</div>
	<!-- 这里监听touchmove事件是为了解决移动端绑定touchstart事件的元素内部无法触摸滚动的问题 -->
```
```JavaScript
	export default{
		data(){
		  return {
        Loop: 0 
		  }
		},
		methods:{
      longTap(e) {
        let touch = e.touches[0];
        this.touchY = touch.clientY;
        this.Loop = setTimeout(()=>{
          this.Loop = 0;
          //需要长按执行的代码放在下面
          console.log('长按事件');
        }, 500)
        return false;
      },
      contentScroll(e) { //如果手指有移动，则取消所有事件，此时说明用户只是要移动而不是长按
        let touch = e.touches[0];
        //以Android 4.0.4为首的一些可恶分子却有些不听话:他们的touchend事件没有如预期的那样触发，当只是轻点一下屏幕时，touchend可以正常触发。但是只要当 touchmove 被触发之后，touchend 就不会再被触发了，而且 touchmove 也没有持续触发。通过阻止默认事件来解决。
        if (Math.abs(touch.clientY - this.touchY) < 10) {
          e.preventDefault();
        }
        clearTimeout(this.Loop); //清除计时器 
        this.Loop = 0; //清除标识位
      },			
      shortTap() {
        clearTimeout(this.Loop);
        if (this.Loop !== 0) {
          //需要点击执行的代码放在下面
          console.log('点击事件');
        }
        return false;
      }			
    }
  }
```