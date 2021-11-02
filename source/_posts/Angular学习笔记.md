---
title: AngularJS学习笔记
abbrlink: 16107
date: 2020-06-09 9:03:41
description: Angular环境搭建、创建项目、运行项目、基本语法。
cover: /images/angularjs_cover.jpg
tags:
	- 前端
	- AngularJS
categories: AngularJS
top_img: /images/default_cover.jpg
---
## 环境搭建

### 全局安装angular/cli

```CMD
npm install -g @angular/cli
```

### 查看版本

```CMD
ng v
```

## 创建项目

```CMD
ng new 项目名称
```

## 运行项目

```CMD
ng serve 或 ng serve --open（自动打开浏览器）
```

## 查看可创建的控件

```CMD
ng g
```

## 创建自定义组件

```CMD
ng g component 目录+组件名
例：ng g component components/home
```

## 使用组件

### 在home.component.html中填充内容
```HTML
<h2>我是首页</h2>
```

### 在根组件中使用
```HTML
<div>
  <app-home></app-home>
  <p>hello world!</p>
</div>
```

## 声明属性的几种方式

* public 共有（默认） 可以在当前类里使用, 也可以在类外面使用
* protected 保护类型 只有在当前类和它的子类里使用

* private 私有 只有在当前类里可以使用

## 声明属性

### 规范写法

```JAVASCRIPT
public message:any = 'hello world';
public userInfo:object = {
  userName : 'leave',
  age : 22
};
```

### 简写

```JAVASCRIPT
message = 'hello world';
userInfo:object = {
  name : 'leave',
  age : 22
};
```

## 普通循环 *ngFor

```JAVASCRIPT
public arr:any[] = ['北京','上海','深圳'];
或者
public arr:Array<any> = ['北京','上海','深圳'];
```

```HTML
<p *ngFor="let item of arr; let i = index">{{item}}{{i}}</p>
或者
<p *ngFor="let item of arr index as i">{{item}}{{i}}</p>
```

## 条件判断 *ngIf *ngSwitch

### *ngIf

```JAVASCRIPT
public flag:boolean = true;
```

```HTML
<div>
  <img *ngIf="flag;else notActive" src="assets/image/home_active.svg">
  <ng-template #notActive>
    <img src="assets/image/home.svg">
  </ng-template>
</div>
```

### *ngSwitch

```JAVASCRIPT
public status:number = 2;
```

```HTML
<div [ngSwitch]="status">
  <div *ngSwitchCase="1">早上</div>
  <div *ngSwitchCase="2">中午</div>
  <div *ngSwitchCase="3">晚上</div>
  <div *ngSwitchDefault>其他</div>
</div>
```
