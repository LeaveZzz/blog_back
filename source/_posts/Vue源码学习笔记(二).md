---
title: Vue源码学习笔记(二)
description: 虚拟DOM和diff算法
cover: /images/vue_cover.png
tags:
  - 前端
  - Vue
categories: JavaScript
top_img: /images/default_cover.jpg
abbrlink: ed00a78e
date: 2021-08-04 19:34:08
---

## 前言

- `diff`：精细化对比，最小量更新（`diff` 算法是发生在虚拟 `DOM` 上的）。
- 虚拟`DOM`：用 `js` 对象描述 `DOM` 的层次结构。`DOM` 中的一切属性都在虚拟 `DOM` 中有对应的属性。

比如有如下真实 DOM

```html
<div class="box">
  <h3>标题</h3>
  <ul>
    <li>七里香</li>
    <li>东风破</li>
  </ul>
</div>
```

对应的虚拟 DOM 则为

```json
{
  "sel": "div",
  "data": {
    "class": { "box": true }
  },
  "children": [
    {
      "sel": "h3",
      "data": {},
      "text": "标题"
    },
    {
      "sel": "ul",
      "data": {},
      "children": [
        { "sel": "li", "data": {}, "text": "七里香" },
        { "sel": "li", "data": {}, "text": "东风破" }
      ]
    }
  ]
}
```

## snabbdom介绍

著名的虚拟 `DOM` 库，是 `diff` 算法的鼻祖，`vue` 就借鉴了 `snabbdom`。在 `github` 上的 `snabbdom` 的源码是用 `ts` 写的，要是直接使用 `build` 出来的 `js` 版的 `snabbdom` 库，可以从 `npm` 上下载。记录本篇笔记时安装的 `snabbdom` 的版本是 `^3.0.3`。
