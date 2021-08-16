---
title: Vue源码学习笔记(一)
description: mustache模板引擎
cover: /images/vue_cover.png
tags:
  - 前端
  - Vue
categories: JavaScript
top_img: /images/default_cover.jpg
abbrlink: 2684979a
date: 2021-07-31 10:30:11
---

## `mustache`模板引擎

> 什么是模板引擎?
> 模板引擎是将数据变为视图最优雅的解决方案。

## 历史上曾经出现的数据变为视图的方法

```json
//数据 data
[
  { "name": "小明", "age": 12, "sex": "男" },
  { "name": "小红", "age": 11, "sex": "女" },
  { "name": "小强", "age": 13, "sex": "男" }
]
```

```html
<!-- 视图 -->
<ul>
  <li>
    <div class="hd">小明的基本信息</div>
    <div class="bd">
      <p>姓名:小明</p>
      <p>年龄:12</p>
      <p>性别:男</p>
    </div>
  </li>
  <li>
    <div class="hd">小红的基本信息</div>
    <div class="bd">
      <p>姓名:小红</p>
      <p>年龄:11</p>
      <p>性别:女</p>
    </div>
  </li>
  …………
</ul>
```

### 纯`DOM`法

非常笨拙，没有实战价值

```html
<ul id="list"></ul>
<script>
  let list = document.getElementById('list');
  for (let i = 0; i < data.length; i++) {
    //每遍历一项,都要用DOM方法去创建li标签
    let oLi = document.createElement('li');
    //创建hd这个div
    let hdDiv = document.createElement('div');
    hdDiv.className = 'hd';
    hdDiv.innerText = data[i].name + '的基本信息';
    //创建bd这个div
    let bdDiv = document.createElement('div');
    bdDiv.className = 'bd';
    //创建三个p
    let p1 = document.createElement('p');
    p1.innerText = '姓名:' + data[i].name;
    bdDiv.appendChild(p1);
    let p2 = document.createElement('p');
    p2.innerText = '年龄:' + data[i].age;
    bdDiv.appendChild(p2);
    let p3 = document.createElement('p');
    p3.innerText = '性别:' + data[i].sex;
    bdDiv.appendChild(p3);
    oLi.appendChild(hdDiv);
    oLi.appendChild(bdDiv);
    list.appendChild(oLi);
  }
</script>
```

### 数组`join`法

曾几何时非常流行，是曾经的前端必会知识

```html
<ul id="list"></ul>
<script>
  let list = document.getElementById('list');
  for (let i = 0; i < data.length; i++) {
    list.innerHTML += [
      '<li>',
      '  <div class="hd">' + data[i].name + '的基本信息</div>',
      '  <div class="bd">',
      '    <p>姓名:' + data[i].name + '</p>',
      '    <p>年龄:' + data[i].age + '</p>',
      '    <p>性别:' + data[i].sex + '</p>',
      '  </div>',
      '</li>',
    ].join('');
  }
</>
```

### `ES6`的反引号法

`ES6` 中新增的`` `${a}` ``语法糖，很好用

```html
<ul id="list"></ul>

<!-- 引入mustache库-->
<script src="./mustache.js">
  </>
  <script>
    let list = document.getElementById('list');
    for (let i = 0; i < data.length; i++) {
      list.innerHTML += `
        <li>
          <div class="hd">${data[i].name}的基本信息</div>
          <div class="bd">
            <p>姓名:${data[i].name}</p>
            <p>年龄:${data[i].age}</p>
            <p>性别:${data[i].sex}</p>
         </div>
        </li>
      `;
    }
</script>
```

## mustache 模板语法基本使用

```html
<div id="container"></div>

<!-- 引入mustache库-->
<script src="./mustache.js"></script>
<script>
  let templateStr = `
      <p>今天是{{weather}},我去{{toDo}}<p>
      <div>
        <p>姓名:{{info.name}}</p>
        <p>年龄:{{info.age}}</p>
        <p>爱好:
          {{#info.hobbies}}
            <span>{{.}}</span>
          {{/info.hobbies}}
        </p>
        <p>
          教育经历:
          {{#info.experience}}
            <span>{{time}}---{{thing}}</span>
          {{/info.experience}}
        </p>
      <div>
    `;
  let data = {
    weather: '晴天',
    toDo: '卖水果',
    info: {
      name: 'Leo',
      age: 18,
      hobbies: ['打篮球', '游泳', '绘画'],
      experience: [
        { time: 2004, thing: '上小学' },
        { time: 2010, thing: '上初中' },
      ],
    },
  };
  let domStr = Mustache.render(templateStr, data);
  let container = document.getElementById('container');
  container.innerHTML = domStr;
</script>
```

```html
<!-- 在模板字符串没出现之前,通过script标签编写 -->
<!-- 这里type中可以随意输入 type="qaq" -->
<script type="text/template" id="myTemple">
  <p>今天是{{weather}},我去{{toDo}}</p>
  <div>
    <p>姓名:{{info.name}}</p>
    <p>年龄:{{info.age}}</p>
    <p>爱好:
      {{#info.hobbies}}
        <span>{{.}}</span>
      {{/info.hobbies}}
    </p>
    <p>
      教育经历:
      {{#info.experience}}
        <span>{{time}}---{{thing}}</span>
      {{/info.experience}}
    </p>
  <div>
</script>
<script>
  let data = {
    weather: '晴天',
    toDo: '卖水果',
    info: {
      name: 'Leo',
      age: 18,
      hobbies: ['打篮球', '游泳', '绘画'],
      experience: [
        { time: 2004, thing: '上小学' },
        { time: 2010, thing: '上初中' },
      ],
    },
  };
  //这里通过ID获取script标签中的模板字符
  let templateStr = document.getElementById('myTemple').innerHTML;
  let domStr = Mustache.render(templateStr, data);
  let container = document.getElementById('container');
  container.innerHTML = domStr;
</script>
```

## 正则表达式实现简单模板数据填充

```js
let templateStr = '<p>今天是{{weather}},我去{{toDo}}<p>';
let data = {
  weather: '晴天',
  toDo: '打篮球',
};
function render(template, data) {
  return template.replace(/\{\{(\w+)\}\}/g, (str, $1) => {
    return data[$1];
  });
}

console.log(render(templateStr, data)); //<p>今天是晴天,我去打篮球<p>
```

## mustache 库的机理

模板字符串-- `编译` --> tokens -- `解析` --> <span style="color:skyblue">dom 字符串</span> <-- `结合` --数据

### 什么是`tokens`?

- tokens 是一个 JS 的嵌套数组,是模板字符串的 JS 表示。
- 它是“抽象语法树”、“虚拟节点”等等的开山鼻祖。

```json
//模板字符串
<p>今天是{{weather}},我去{{toDo}}。<p>

//tokens,是一个嵌套数组,每一项都是一个token
[
  ["text","<p>今天是"], //token
  ["name","weather"], //token
  ["text",",我去"], //token
  ["name","toDo"], //token
  ["text","。<p>"] //token
]

```

当模板字符串中有循环存在时,它将被编译为嵌套更深的 tokens

```json
//模板字符串
<div>
  <ul>
    {{#list}}
    <li>{{.}}</li>
    {{/list}}
  </ul>
</div>

//tokens
[
  ["text","<div><ul>"],
  ["#","list",[
    ["text","<li>"],
    ["name","."],
    ["text","</li>"]
  ]],
  ["text","</ul></div>"]
]
```

### mustache 底层做的两件事

- 将模板字符串编译为`tokens`形式。
- 将`tokens`结合数据，解析为`dom`字符串。

了解完 tokens，现在开始自己写一个 LEO_TemplateEngine 对象来实现 Mustache 的功能。我们将把独立的功能拆写为独立的 js 文件，通常是一个独立的类，每个单独的功能都应能完成独立的单元测试。

## 手写实现 mustache

### 环境配置

1. 初始化项目
   `npm init`
2. 安装 webpack
   `npm i webpack@4.44.2 webpack-cli@3.3.12 webpack-dev-server@3.11.0`
3. 创建 webpack.config.js 文件配置 webpack
```javascript
//webpack.config.js
const path = require('path');
module.exports = {
  //模式 开发
  mode: 'development',
  //入口
  entry: './src/index.js',
  //出口
  output: {
    filename: 'bundle.js',
  },
  //配置webpack-dev-server
  devServer: {
    //静态文件根目录
    contentBase: path.join(__dirname, 'www'),
    //不压缩
    compress: false,
    //端口号
    port: 8080,
    //虚拟打包路径,bundle.js文件没有真正生成
    publicPath: '/virtual/',
  },
};
```

4. 将 webpack-dev-server 配置到脚本命令，然后运行
   `npm run dev`

### 手写实现 Scanner 类

`Scanner`类的实例就是一个扫描器，用来扫描构造时作为参数提供的那个模板字符串。

- 属性
  `pos`：指针，用于记录当前扫描到字符串的位置
  `tail`：尾巴，值为当前指针之后的字符串（包括指针当前指向的那个字符）

- 方法
  `scan`：无返回值，让指针跳过传入的结束标识 `stopTag`
  `scanUntil`：传入一个指定内容`stopTag`作为让指针`pos`结束扫描的标识，并返回扫描内容

```javascript
//scanner.js
export default class Scanner {
  constructor(templateStr) {
    this.templateStr = templateStr;
    this.pos = 0;
    this.tail = templateStr;
  }
  //功能弱,走过指定内容,没有返回值
  scan(tag) {
    if (this.tail.indexOf(tag) != 0) return;
    //tag有多长,比如{{长度是2,就让指针后移多少位
    this.pos += tag.length;
    this.tail = this.templateStr.substring(this.pos);
  }
  //让指针进行扫描,直到遇见指定内容结束,并且能够返回结束之前路过的文字
  scanUntil(stopTag) {
    const pos_backup = this.pos;
    //当尾巴的开头不是stopTag的时候,就说明还没有扫描到stopTag
    while (this.eos() && this.tail.indexOf(stopTag) != 0) {
      this.pos++;
      //改变尾巴为从当前指针这个字符开始,到最后的全部字符
      this.tail = this.templateStr.substring(this.pos);
    }
    return this.templateStr.substring(pos_backup, this.pos) || null;
  }
  //指针是否已经到头,返回布尔值
  eos() {
    return this.pos < this.templateStr.length;
  }
}
```

### 根据模板字符串生成 tokens

有了`Scanner`类后，就可以着手去根据传入的模板字符串生成一个`tokens`数组了。最终想要生成的`tokens`里的每一条`token`数组的第一项用`name(数据)`或`text(非数据文本)`或`#(循环开始)`或`/(循环结束)`作为标识符。
新建一个`parseTemplateToTokens.js` 文件来实现

```javascript
//parseTemplateToTokens.js
import Scanner from './scanner';
import nestTokens from './nestTokens';
export default function (templateStr) {
  let scanner = new Scanner(templateStr);
  let tokens = [];
  let stack = [];
  let collector = tokens;
  while (scanner.eos()) {
    let words = scanner.scanUntil('{{');
    word && tokens.push(['text', words]); // 保证 word 有值再往 tokens 里添加
    scanner.scan('{{');
    words = scanner.scanUntil('}}');
    if (words) {
      /**
       *  判断从 {{ 和 }} 之间收集到的 word 的开头是不是特殊字符 # 或 /,
       *  如果是则这个 token 的第一个元素相应的为 # 或 /, 否则为 name
       */
      switch (words[0]) {
        case '#':
          tokens.push([words.substring(0, 1), words.substring(1)]);
          break;
        case '/':
          tokens.push([words.substring(0, 1), words.substring(1)]);
          break;
        default:
          tokens.push(['name', words]);
          break;
      }
    }
    scanner.scan('}}');
  }
  return nestTokens(tokens);
}
```

目前已经实现将简单模板转换成`tokens`，接下去，就是要想办法，让循环的部分，也就是`#`和`/`之间的内容，作为以`#`为数组第 1 项元素的那个`token`的第 3 项元素。也就是把红框里的内容插入到`["#","arr"]`内，成为其第 3 项元素；同理，将蓝框里的内容插入到`["#","hobbies"]`内成为其第 3 项元素。

### 实现 tokens 的嵌套

新建`nestTokens.js`文件，定义`nestTokens`函数来做`tokens`的嵌套功能，将传入的`tokens`处理成包含嵌套的`nestTokens`数组返回。

**实现思路:**
在`nestTokens`中，我们遍历传入的`tokens`的每一个`token`，遇到第一项是`#`和 `/` 的分别做处理，其余的做一个默认处理。大致思路是当遍历到的`token`的第一项为 # 时，就把直至遇到配套的 `/` 之前，遍历到的每一个`token`都放入一个容器（`collector`）中，把这个容器放入当前`token`里作为第 3 项元素。
**但这里有个问题：在遇到匹配的`/`之前又遇到`#`了怎么办？也就是如何解决循环里面嵌套循环的情况？**
解决的思路是新建一个`栈数据类型`的数组（`stack`），遇到一个`#`，就把当前 `token`放入这个栈中，让 `collector` 指向这个 `token`的第三个元素。遇到下一个`#`就把新的`token`放入栈中，`collector`指向新的`token`的第三个元素。遇到 `/` 就把栈顶的`token`移出栈，`collector` 指向移出完后的栈顶 `token`。这就利用了栈的`先进后出`的特点，保证了遍历的每个`token`都能放在正确的地方，也就是`collector`都能指向正确的地址。

```javascript
//nestTokens.js
export default function (tokens) {
  let nestTokens = [];
  let stack = [];
  let collector = nestTokens; //一开始让收集器 collector 指向最终返回的数组 nestTokens
  tokens.forEach((token) => {
    switch (token[0]) {
      case '#':
        collector.push(token);
        stack.push(token[1]);
        collector = token[2] = []; //连等赋值 token[2] = [];collector = token[2];
        break;
      case '/':
        stack.pop();
        collector = nestTokens;
        break;
      default:
        collector.push([token[0], token[1]]);
        break;
    }
  });
  return nestTokens;
}
```

```javascript
//上面用到的连等赋值暗含小坑
let a = { n: 1 };
a.x = a = { n: 2 };
console.log(a.x); // 输出 undefined
```

`a.x`之所以为`undefined`的关键原因在于`js`的运算中`.`和`=`运算符同时出现，会先执行`.`运算，
所以`a.x = a = {n:2}`这一步是先给`a`增加了一个属性`x`，而此时`a`还是指向`{n:1}`对象。也就是说原来的`{n:1}`对象现在变成了`{n:1, x:a}`，之后`a`被重新赋值了指向了`{n:2}`对象，所以`{n:1, x:a}`就没有任何变量引用被`GC(Garbage Collection垃圾回收)`了。故而`a.x`的值是`undefined`。

### 手写将 tokens 注入数据

我们已经成功实现了将模板字符串编译为`tokens`的过程，剩下的工作就是将`tokens`结合数据解析成`dom`字符串。

大致思路是遍历`tokens`数组，根据每条`token`的第一项的值来做不同的处理，为 text 就直接把`token[1]`加入到最终输出的`dom`字符串，为`name`则根据`token[1]`去`data`里获取数据，结合进来。
当`data`里存在多层嵌套的数据结构，比如`data = { test: { a: { b: 10 } }`，这时如果某个`token`为`["name", "test.a.b"]`，即代表数据的`token`的第 2 项元素是`test.a.b`这样的有多个点符号的值，那么我么直接通过`data[test.a.b]`是无法拿到正确的值的，因为`js`不认识这种写法。我们需要提前准备一个 lookup 函数，用以正确获取数据。

```javascript
//lookup.js
export default function (dataObj, keyName) {
  // 如果传入的keyName不包含.符号,直接返回
  if (keyName == '.') return dataObj[keyName];
  let keys = keyName.split('.'); //将keyName分割成一个数组
  return keys.reduce((pre, it) => {
    return pre[it];
  }, dataObj); //一步步获取

  //或者
  if (keyName == '.') return dataObj[keyName];
  return eval(`dataObj.${keyName}`);
  //eval()函数可计算某个字符串，并执行其中的的JavaScript代码。
}
```

接下来就可以开始写个`renderTemplate`函数将`tokens`和`data`作为参数传入，解析为`dom`字符串了。

```javascript
//renderTemplate.js
import lookup from './lookup';
import parseArray from './parseArray';
export default function (tokens, data) {
  let domStr = '';
  tokens.forEach((token) => {
    switch (token[0]) {
      case 'text':
        domStr += token[1];
        break;
      case 'name':
        domStr += lookup(data, token[1]);
        break;
      default:
        domStr += parseArray(lookup(data, token[1]), token[2]);
        break;
    }
  });
  return domStr;
}
```

需要注意的是遇到循环的情况，也就是当某个`token`的第一项为`#`时，要再次递归调用`renderTemplate`函数。这里我们新定义了一个`parseArray`函数来处理。

```javascript
//parseArrary.js
import lookup from './lookup';
import renderTemplate from './renderTemplate';
export default function (dataObj, tokens) {
  let domStr = '';
  dataObj.forEach((item) => {
    domStr += renderTemplate(tokens, {
      ...item,
      '.': item, //针对简单数组的情况，即模板字符串里的 {{.}}
    });
  });
  return domStr;
}
```

现在已经完成了一个简易版的`mustache`库，下面就可以开始使用它了。
```javascript
//index.js
import parseTemplateToTokens from './parseTemplateToTokens';
import renderTemplate from './renderTemplate';
window.LEO_TemplateEngine = {
  render(templateStr, data) {
    let tokens = parseTemplateToTokens(templateStr);
    let domStr = renderTemplate(tokens, data);
    return domStr;
  },
};
```

```html
<div id="container"></div>
<script src="/virtual/bundle.js"></script>
<script>
  let templateStr = `
        <p>今天是{{weather}},我去{{toDo}}</p>
        <div>
          <p>姓名:{{info.name}}</p>
          <p>年龄:{{info.age}}</p>
          <p>爱好:
            {{#info.hobbies}}
              <span>{{.}}</span>
            {{/info.hobbies}}
          </p>
          <p>
            教育经历:
            {{#info.experience}}
              <p>{{time}}</p>
              {{#thing}}
              <span>{{.}}</span>
              {{/thing}}
            {{/info.experience}}
          </p>
        <div>
      `;
  let data = {
    weather: '晴天',
    toDo: '卖水果',
    info: {
      name: 'Leo',
      age: 18,
      hobbies: ['打篮球', '游泳', '绘画'],
      experience: [
        { time: 2004, thing: ['参加篮球比赛', '参加乒乓球比赛'] },
        { time: 2010, thing: ['参加足球比赛'] },
      ],
    },
  };
  let domStr = LEO_TemplateEngine.render(templateStr, data);
  let container = document.getElementById('container');
  container.innerHTML = domStr;
</script>
```
