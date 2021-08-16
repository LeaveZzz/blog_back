---
title: TypeScript学习笔记
description: 数据类型、函数、类、接口、泛型、命名空间、装饰器。
cover: /images/typescript_cover.jpg
tags:
  - 前端
  - TypeScript
categories: TypeScript
top_img: /images/default_cover.jpg
abbrlink: 4a9ed04d
date: 2021-07-30 01:32:34
---

## 数据类型

`TypeScript`中为了使编写的代码更规范，增加了类型校验

```javascript
// js:类型变化不报错
let flag = true;
flag = 123;
// ts:必须指定类型
let flag: boolean = true;
flag = 123; //报错
```

### 布尔类型(`boolean`)

```javascript
let flag: boolean = true;
```

### 数字类型(`number`)

```javascript
let num: number = 7;
```

### 字符串类型(`string`)

```javascript
let str: string = 'hello world!';
```

### 数组类型(`array`)

```javascript
//写法一
let arr1: number[] = [1, 2, 3, 4, 5];
let arr2: string[] = ['Angular', 'React', 'Vue'];
let arr3: any[] = ['hi', 123, false];
//写法二
let arr4: Array<number> = [1, 2, 3, 4, 5];
```

### 元组类型(`tuple`)

元组属于数组的一种，可以指定数组内值的类型(元素顺序和个数必须与定义的保持一致)

```javascript
let arr: [string, number, boolean] = ['hi', 123, false];
```

### 枚举类型(`enum`)

常用来标识状态码

```javascript
enum Flag{
  success=1,
  error=-1
}
let f:Flag=Flag.error; //-1

//如果标识符没有赋值，那么打印的就是下标
enum Coloe {
  red,
  blue='#0000FF',
  orange
}
let c1:Color = Color.red; //0
let c2:Color = Color.blue; //'#0000FF'
let c3:Color = Color.orange; //2
```

### 任意类型(`any`)

```javascript
let num: any = 123;
num = true; //不报错
```

### `null` 和 `undefined`

`null` 和 `undefined`是其他数据类型(`never`类型)的子类型

```javascript
//变量定义之后没有赋值，编译时会报错
let a: number;
console.log(a); //error TS2454: letiable 'a' is used before being assigned.

// 一个元素可能是number类型，可能是null或者undefined
let b: number | null | undefined;
console.log(b); //无报错，打印undefined
```

### `void`类型

`void`表示没有任何类型，一般用于一个方法没有返回值的情况，值得注意的是，方法没有返回值将得到`undefined`，但是我们需要定义成`void`类型，而不是`undefined`类型，否则将报错。

```javascript
//如果方法没有返回值
function run(): void {
  console.log('abc');
}
//如果方法有返回值
function run(): number {
  return 1;
}
```

### `never`类型

- `never`表示的是那些永不存在的值的类型，包括`null`和`undefined`。
- 在一个没有返回值标注的函数表达式或箭头函数中,如果函数没有`return`语句,或者仅有表达式类型为`never`的`return`语句,并且函数的终止点无法被执行到(按照控制流分析), 则推导出的函数返回值类型是`never`。

```javascript
//返回never的函数必须有无法被执行到的终止点
let err = (): never => {
  throw new Error('异常');
};

let infiniteLoop = (): never => {
  while (true) {}
};
```

## 函数

### 函数的定义

```javascript
//函数声明法
function getStr(): string {
  return 'hello world!';
}
//匿名函数
let gerNum = function (): number {
  return 123;
};
//箭头函数
let getFlag = (): boolean => {
  return false;
};
```

### 函数中传参

```javascript
//定义参数的数据类型
function getInfo(name: string, age: number): string {
  return `${name}---${age}`;
}
getInfo('Leo', 18); //Leo---18
getInfo('Leo'); //编译报错

//可选参数(可选参数必须放在参数的最后面)
function getInfo(name: string, age?: number): string {
  return age ? `${name}---${age}` : `${name}---年龄保密`;
}
getInfo('Leo', 18); //Leo---18
getInfo('Leo'); //Leo---年龄保密

//默认参数
function getInfo(name: string = 'Leo', age: number = 18): string {
  return `${name}---${age}`;
}
getInfo(); //Leo---18

//剩余参数
function getSum(...args: number[]) {
  return args.reduce((pre, item) => pre + item, 0);
}
getSum(1, 2, 3, 4, 5); //15
```

### 函数重载

- 方法重载(`overload`)在传统的静态类型语言中是很常见的。JavaScript 作为动态语言， 是没有重载这一说的。一是它的参数没有类型的区分，二是对参数个数也没有检查。虽然语言层面无法自动进行重载，但借助其动态的特性，我们可以在代码中手动检查入参的类型，或者通过`arguments`获取到参数个数，从而实现根据不同的入参做不同的操作。

- 比如有一个获取聊天消息的方法，根据传入的参数从数组中查找数据。如果入参为数字，则认为是 id，然后从数据源中找对应 id 的数据并返回，否则当成类型，返回这一类型的消息。

```javascript
//TypeScript中,假如我们的消息数据为如下结构：
type MessageType = 'string' | 'image' | 'audio';
type Message = {
  id: number,
  type: MessageType,
  content: string,
};
let data: Message[] = [
  { id: 0, type: 'string', content: 'hello' },
  { id: 1, type: 'image', content: 'url_for_iamge' },
  { id: 2, type: 'string', content: 'world' },
];
```

```javascript
//不使用重载实现
function getMessage(
  query: number | MessageType
): Message[] | Message | undefined {
  if (typeof query === 'number') {
    return data.find(message => message.id === query);
  } else {
    return data.filter(message => message.type === query);
  }
}

//这样做有两个缺点一是类型书写上不太优雅，二是没有发挥出TypeScript类型检查的优势，这里我们是可以根据入参的类型明确知道返回的类型的，即如果传入的是id，返回的是单个数据或undefined，如果是根据类型查找，返回的是数组。而现在调用方法后，得到的类型太过宽泛，这和使用any 做为返回没多大差别。
//因为类型的不明朗，返回的结果都不能直接操作，需要进行类型转换后才能继续，否则编译会报错。
let result1 = getMessage('audio');
console.log((result1 as Message[]).length); //不能直接对result1调用数组方法

let result2 = getMessage(1);
if (result2) {
  console.log((result2 as Message).content); //不能对result2直接访问消息对象中的属性
}
```

```javascript
//使用重载实现
//通过提供多个函数类型的声明来解决上面的问题，最后得到的结果就是间接实现了函数的重载。当然这个重载只是TypeScript编译时的。
function getMessage(id: number): Message | undefined;
function getMessage(type: MessageType): Message[];
function getMessage(query: any): any {
  if (typeof query === 'number') {
    return data.find(message => message.id === query);
  } else {
    return data.filter(message => message.type === query);
  }
}

//得到的结果类型是重载方法中指定的入参与返回的组合，在对结果进行使用时，无须再进行类型转换
const result1 = getMessage('audio');
console.log(result1.length); //无须类型转换

const result2 = getMessage(1);
if (result2) {
  console.log(result2.content); //无须类型转换
}
```

## 类

### 回顾：ES5 中的类

```javascript
//定义类
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.getInfo = function () {
    console.log(`姓名:${this.name},年龄:${this.age}`);
  };
}
//原型上定义属性和方法
Person.prototype.sex = '男';
Person.prototype.sayHi = function () {
  console.log('hi~');
};
```

```javascript
//对象冒充继承
function Inherit(name, age) {
  Person.call(this, name, age); //可以继承构造函数里面的属性和方法,实例化子类可以给父类传参,但是无法继承原型(Person.prototype)上的属性和方法。
}
let i = new Inherit('Leo', 18);
//new关键字做了什么?
//1> let obj = {};
//2> obj.__proto__ = Inherit.prototype;
//3> let i = Inherit.call(obj,'Leo',18);
i.getInfo(); //姓名:Leo,年龄:18
i.sayHi(); //无法继承原型上的属性和方法 i.sayHi is not a function
```

```javascript
//原型链继承
function Inherit(name, age) {}
Inherit.prototype = new Person(); //可以继承构造函数和原型里面的属性和方法,但是实例化子类时无法给父类传参。
//Inherit.prototype.__proto__ = Person.prototype;
let i = new Inherit('Leo', 18);
i.sayHi(); //hi~
i.getInfo(); //无法给父类传参 姓名:undefined,年龄:undefined
```

```javascript
//原型链+对象冒充组合使用
function Inherit(name,age){
  Person.call(this,name,age);/
}
Inherit.prototype = new Person();
let i = new Inherit('Leo',18);
i.sayHi(); //hi~
i.getInfo(); //i.sayHi is not a function
```

### 修饰符

```javascript
ts里面定义属性的时候给我们提供了三种修饰符：
  public(默认):公有,在类里面、子类、类外部都可以访问
  protected:保护,在类里面、子类里面可以访问,在类外部无法访问
  private:私有,在类里面可以访问,子类、类外部没法访问
  注：属性如果没有显示声明修饰符，则默认为 public
```

```javascript
class Person{
  public name: string;
  constructor(name:string){
    this.name = name; //类里使用
  }
  getName():string{
    return this.name;
  }
}
let p = new Person('Leo');
console.log(p.name); //类外部使用
class Inherit extends Person {
  constructor(name: string) {
    super(name);
  }
  setName(name:string):void{
    this.name = name; //子类里使用
  }
}
let i = new Inherit('小明');
console.log(i.name); //类外部使用
```

### 定义类

```javascript
//定义类
class Person {
  name: string; //这里省略了public
  constructor(name: string) {
    //构造函数,实例化类时触发的方法
    this.name = name;
  }
  getName(): string {
    return this.name;
  }
  setName(name: string): void {
    this.name = name;
  }
}
let p = new Person('Leo');
console.log(p.getName());
p.setName('小明');
console.log(p.getName());
```

### 静态属性 & 静态方法

通过`static`关键字声明静态属性 & 静态方法，通过`类名.属性名`或者`类名.方法名`直接调用。

```javascript
class Person {
  public name: string;
  public age: number = 20;

  //静态属性
  static sex = '男';

  constructor(name: string) {
    this.name = name;
  }

  run() {
    console.log(`${this.name}在跑步。`);
  }

  //静态方法
  static print() {
    console.log('这是静态方法print');
    //静态方法里面没法直接调用类里面的属性
    console.log(this.age); //undefined
    //静态属性调用
    console.log(Person.sex); //男
  }
}
let p = new Person('张三');
p.run(); //张三在跑步。
//静态方法调用
Person.print();
```

### 继承

使用`extends`和`super`关键字实现继承。

```javascript
class Inherit extends Person {
  constructor(name: string) {
    super(name);
  }
}
let i = new Inherit('小明');
console.log(i.getName());
```

### 多态

父类定义一个方法不去实现，让继承它的子类去实现，每一个子类有不同的表现。多态属于继承。

```javascript
//父类 Animal
class Animal {
  public name: string;
  constructor(name: string) {
    this.name = name;
  }
//定义一个方法,具体不做实现,子类自己实现。
  eat() {
    console.log('function: eat');
  }
}

//子类 Dog
class Dog  extends Animal {
  constructor(name: string) {
    super(name);
  }
  //自行实现父类定义的eat方法
  eat() {
    console.log(`${this.name}吃肉。`);
  }
}

//子类 Cat
class Cat extends Animal {
  constructor(name: string) {
    super(name);
  }
  //自行实现父类定义的eat方法
  eat() {
    console.log(`${this.name}吃鱼。`);
  }
}

let d = new Dog('小狗');
d.eat();  //小狗吃肉。
let c = new Cat('小猫');
c.eat();  //小猫吃鱼。
```

### 抽象类 & 抽象方法：

- 使用`abstract`关键字定义抽象类和抽象方法，抽象方法只能放在抽象类里面。
- ts 中的抽象类，是提供其他类继承的基类，不能直接被实例化。抽象类中的抽象方法不包含具体实现且必须在派生类中实现。
- 抽象类和抽象方法用来定义标准。

```javascript
//抽象类 Animal
abstract class Animal {
    public name: string;
    constructor(name: string) {
        this.name = name;
    }
    //抽象方法,子类必须实现这个方法
    abstract eat(): any;

    //普通方法,不要求子类必须实现
    sleep() {
        console.log(`${this.name}在睡觉。`);
    }
}

let a = new Animal();  // error: 无法创建抽象类的实例

//子类 Dog 继承 抽象类 Animal
class Dog extends Animal {
    constructor(name: string) {
        super(name);
    }
    //抽象类的子类(派生类)必须实现抽象类里的抽象方法,如果不实现会报错。
    eat() {
        console.log(`${this.name}吃肉。`);
    }
}

let d = new Dog('小狗');
d.eat();  //小狗吃肉。
```

## 接口

`TypeScript`的核心原则之一是对值所具有的结构进行类型检查，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。

### 定义接口 & 基本使用

```javascript
interface LabelledValue {
  color: string;
  height: number;
}

//赋值的时候,变量的形状必须和接口的规则保持一致
const labelVal: LabelledValue = {
  color: '灰色',
  height: 56,
};

//对方法传入的参数进行约束
function printLabelVal(label: LabelledValue) {
  console.log(`${label.color}---${label.height}`);
}

//调用方式一,调用方法编译报错,传入的参数不符合接口规范
printLabelVal({
  color: '天蓝色',
  height: 88,
  width: 100,
});

// 调用方式二,这样传参不报错,暂时不知道具体原理,之后再来填坑
let obj = {
  color: '天蓝色',
  height: 88,
  width: 100,
};
printLabelVal(obj);
```

### 可选属性 & 只读属性

```javascript
//可选属性:有时我们希望不需要完全匹配一个接口，那么可以用可选属性
interface Person {
name: string;
age: number;
car?: string;
}
let Lucy: Person = {
name: 'Lucy Lucy',
age: 18,
//car: '宝马'
};


//只读属性:一些对象属性只能在对象刚刚创建的时候修改其值。你可以在属性名前用 readonly来指定只读属性。
interface Point {
readonly x: number;
readonly y: number;
}

let p1: Point = { x: 10, y: 20 };
p1.x = 5; //编译报错 Cannot assign to 'x' because it is a read-only property
```

### 函数类型

对方法传入的参数、以及返回值进行约束。

```javascript
interface MyTypeFn {
  (x: number, y: number): number;
}
let add: MyTypeFn = function (x: number, y: number): number {
  return x + y;
};
```

### 可索引属性

```javascript
//对数组的约束,数组类型接口
interface UserArray {
  //表示数组中index必须是number,value必须是string
  [index: numer]: string;
}
let arr: UserArray = ['123', '456'];
//对对象的约束,对象类型接口
interface UserObj {
  [index: string]: string;
}
let obj: UserObj = { name: '123' };
```

### 类接口

对类的约束，和抽象类有点相似。

```javascript
//定义接口 规定实现类必须要有name属性和eat方法
interface Animal {
  name: string;
  eat(str: string): void;
}
class Dog implements Animal {
  name: string; //若没此属性，ts会编译报错
  constructor(name: string) {
    this.name = name;
  }
  eat() {
    console.log('eat');
  }
}
```

```javascript
//小例子:接口继承接口,类继承类,类实现接口
interface Animal {
  eat(): void;
}

interface Person extends Animal {
  work(): void;
}

class Programmer {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  coding(code: string) {
    console.log(`${this.name}---${code}`);
  }
}

class Demo extends Programmer implements Person {
  name: string;
  constructor(name: string) {
    super(name);
    this.name = name;
  }

  // eat必须定义
  eat() {
    console.log(this.name + '吃');
  }
  // work也必须定义
  work() {
    console.log(this.name + '工作');
  }
}
```

## 泛型

为什么需要`泛型`?

- 软件工程中，我们不仅要创建一致的定义好的 API，同时也要考虑可重用性，组件不仅能够支持当前的数据类型，同时也能支持未来的数据类型，这在创建大型系统时为你提供了十分灵活的功能。
- 在像`c#`和`java`中，可以使用泛型来创建可重用的组件，一个组件可支持多种类型的数据，这样用户就可以以自己的数据类型来使用组件。
- 通俗理解：泛型就是解决类、接口、方法的重用性，以及对不特定数据类型的支持。
- 可以支持不特定的数据类型。
- 和`any`有什么区别?
- `any`放弃了类型检查
- 如果想做到传入什么类型就返回什么类型，例如传入`number`类型就返回`number`类型，这时候就可以使用泛型。

### 泛型函数

```javascript
function fn<T>(arg: T): T {
  return arg;
}
console.log(fn<number>(77));
```

### 泛型类

```javascript
class Person<T> {
  private name: T;
  constructor(name: T) {
    this.name = name;
  }
  getName(): T {
   return this.name;
  }
}
let p = new Person<string>('Leo');
console.log(p.getName());
```

如上，`<T>`表示传递一个 T 类型，在`new`的时候才把具体类型传入。其中`T`是变量可改，但通常比较常见就是写`T`
之前说`TypeScript`类型的时有说到数组，其实数组本质就是一个泛型类

```javascript
let a = new Array<number>();
```

### 泛型接口

```javascript
//写法一
interface Config {
  <T>(value: T): T;
}
let getData: Config = function <T>(value: T): T {
  return value;
};
console.log(getData < string > '张三');

//写法二：此处与写法一的不同之处在于,类型传递的时机不同,上面那个是在函数调用的时候传递类型,此处是在接口实现的时候传递类型
interface Config<T> {
  (value: T): T;
}
let getData: Config<string> = function <T>(value: T): T {
  return value;
};
console.log(getData('张三'));
```

### 把类作为参数来约束数据传入的类型

```javascript
class User {
  username: string | undefined;
  password: string | undefined;
}
class MySqlDb {
  add(user: User): boolean {
    console.log(user);
    return true;
  }
}

// 调用
let u = new User();
u.username = '张三';
u.password = '123456';

let Db = new MySqlDb();
Db.add(u); //{ username: '张三', password: '123456' }
```

```javascript
//上述方法可以改为泛型类
//操作数据库的泛型类，这样可以规范插入数据库数据的类规范
class MySqlDb<T> {
  add(info: T): boolean {
    console.log(info);
    return true;
  }
}

// 想给User表增加数据
// 1、定义一个User类 和数据库进行映射
class User {
  username: string | undefined;
  password: string | undefined;
}
let u = new User();
u.username = '张三';
u.password = '123';
let Db = new MySqlDb<User>(); // 这一步很关键,要定义User类型
Db.add(u);

// 2、文章类，数据库映射
class Article {
  title: string | undefined;
  desc: string | undefined;
  status: number | undefined;
  constructor(params:{
    title:string | undefined;
    desc:string | undefined;
    status?:number | undefined;
  }) {
    Object.assign(this,params)
  }
}

// 调用
let a = new Article({
  title: '分类',
  desc: '123',
  status: 1,
});

//类当前参数的泛型类
let Db = new MySqlDb<Article>(); // 指定类型
Db.add(a); // log a
```

## 命名空间

- 命名空间一个最明确的目的就是解决重名问题。

- 假设这样一种情况，当一个班上有两个名叫小明的学生时，为了明确区分它们，我们在使用名字之外，不得不使用一些额外的信息，比如他们的姓（王小明，李小明），或者他们父母的名字等等。

- 命名空间定义了标识符的可见范围，一个标识符可在多个名字空间中定义，它在不同名字空间中的含义是互不相干的。这样，在一个新的名字空间中可定义任何标识符，它们不会与任何已有的标识符发生冲突，因为已有的定义都处于其他名字空间中。

```javascript
//以上定义了一个命名空间A，如果我们需要在外部可以调用A中的内容，则需要添加export关键字。
namespace A{
  export function eat(food:string):void{
    console.log(`吃${food}`)
  }
}
namespace B{
  A.eat('桃子'); //在另一个命名空间调用
}
```

## 装饰器

- 装饰器是一种特殊类型的声明，他能够被附加到类声明，方法，属性或者参数上，可以修改类的行为。
- 通俗的将装饰器就是一个方法，可以注入到类、方法、属性参数上来扩展类、属性、方法、参数的功能。
- 常见的装饰器有：类装饰器、属性装饰器、方法装饰器、参数装饰器。
- 装饰器的写法：普通装饰器（无法传参）、装饰器工厂（可传参）。
- 装饰器是过去几年中 js 最大的成就之一，已经是 ES7 的标准特性之一。

### 类装饰器

```javascript
//普通装饰器
function logClass(params: any) {
  console.log(params); //params就是当前类
  params.prototype.apiUrl = '动态扩展的属性';
  params.prototype.run = function () {
    console.log('我是一个run方法');
  };
}

@logClass //类装饰器,普通装饰器,无法传参,默认把class传入
class HttpClient {
  constructor() {}
  getData() {}
}
```

```javascript
//装饰器工厂
//作用:修改构造函数 扩展类属性和方法
function logClass(params: string) {
  // params是下方传过来的参数
  return function (target: any) {
    // target相当于是默认传过来的
    console.log(target);
    console.log(params);
    target.prototype.apiUrl = params;
  };
}

@logClass('https://baidu.com') // 可以传参
class HttpClient {
  constructor() {}
  getData() {}
}

let http: any = new HttpClient();
console.log(http.apiUrl); // https://baidu.com
```

```javascript
//可以修改构造函数的写法
function logClass(target: any) {
  console.log(target);
  return class extends target {
    apiUrl: any = '我是修改后的新数据';
    getData() {
      this.apiUrl = this.apiUrl + '@@@';
      console.log(this.apiUrl);
    }
  };
}

@logClass
class HttpClient {
  public apiUrl: string | undefined;
  constructor() {
    this.apiUrl = '我是构造函数里面的apiUrl';
  }
  getData() {
    console.log(this.apiUrl);
  }
}
let http: any = new HttpClient();
http.getData();
```

### 属性装饰器

可以给属性赋值

```javascript
// 类装饰器
function logClass(params: string) {
  // params是下方传过来的参数
  return function (target: any) {
    // target是默认传过来的类对象
    console.log(target);
    console.log(params);
    target.prototype.apiUrl = params;
  }
}

// 属性装饰器
function logProperty(params: any) {
  // 固定写法,参数中:target为类对象,attr为被装饰的属性名称
  return function (target: any, attr: any) {
    console.log(target);
    console.log(attr);
    target[attr] = params;
  };
}

@logClass('https://baidu.com') // 可以传参
class HttpClient {
  //这个属性修饰器的作用就是给url赋值初始值
  @logProperty('http://baidu.com')
  public url: any | undefined;
  constructor() {}
  getData() {}
}

let http: any = new HttpClient();
console.log(http.apiUrl); // https://baidu.com

```

### 方法装饰器

```javascript
//例一
function get(params: any) {
  return function (target: any, methodName: any, desc: any) {
    console.log(target); //对于静态成员来说是类的构造函数,对实例成员来说是类的原型对象
    console.log(methodName); //方法名字,getData
    console.log(desc); //方法的描述对象,desc.value是该方法
    target.apiUrl = 'xxx'; //修改类属性
    target.run = function () {
      console.log('run');
    };
  };
}
class HttpClient {
  public url: any | undefined;
  constructor() {}
  @get('https://www.baidu.com')
  getData() {
    console.log(this.url);
  }
}

let http: any = new HttpClient();
console.log(http.apiUrl); //输出xxx
http.run(); //输出run

```

```javascript
//例二 修改当前的方法(主要作用是装饰方法,并把方法的参数给变换类型)
//这个方法装饰其主要作用就是把参数都给格式化成string类型
function get(params: any) {
  return function (target: any, methodName: any, desc: any) {
    //修改方法,使所有参数变成string类型,并打印
    let oMethod = desc.value;
    desc.value = function (...args: any[]) {
      args = args.map((value) => {
        return String(value);
      });
      //利用apply进行对象冒充,对getdata进行修改,如果没有apply就相当于替换了getData方法
      oMethod.apply(this, args); //this指HttpClient类
    };
  };
}
class HttpClient {
  public url: any | undefined;
  constructor() {}
  @get('https://www.baidu.com')
  getData(...args: any[]) {
    console.log(args);
    console.log('我是getData方法');
  }
}

let http: any = new HttpClient();
http.getData(123, 'xxx'); //['123','xxx'] 我是getData方法

```

### 方法参数装饰器

用的比较少，类装饰器也可以实现这个功能

```javascript
function logParams(params: any) {
  return function (target: any, methodName: any, paramsIndex: any) {
    console.log(params); // xxxx
    console.log(target); // 原型对象
    console.log(methodName); // getData
    console.log(paramsIndex); //参数在函数参数列表中的索引 0
  };
}
class HttpClient {
  public url: any | undefined;
  constructor() {}

  getData(uuid: any, @logParams('xxxx') nanoid: string) {
    console.log(uuid); // iii
  }
}

var a = new HttpClient();
a.getData('iii', 'ooo');


```

### 装饰器执行顺序

当存在多个装饰器时候：从上到下,从右到左。
