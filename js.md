### let 和 var

- let声明的范围是块作用域，而var声明的范围是函数作用域，没有块级作用域的说法
- JavaScript引擎会记录用于变量声明的标识符及其所在的块作用域，因此嵌套使用相同的标识符不会报错，即变量名+块id 确定一个变量
- 使用let在全局作用域中声明的变量不会成为window对象的属性，不过，let声明仍然是在全局作用域中发生的，相应变量会在页面的生命周期内存续。
- let生命的变量存在死区

```javascript
let x = 1;

function func() {
  // 引擎从函数开始就知道局部变量 x，
  // 但是变量 x 一直处于“未初始化”（无法使用）的状态，直到结束 let（“死区”）
  // 因此答案是 error

  console.log(x); // ReferenceError: Cannot access 'x' before initialization

  let x = 2;
}

func();
```

### strict严格模式下

- 严格模式下，当一个函数声明在一个代码块内时，它在该代码块内的任何位置都是可见的。但在代码块外不可见。
- 在非严格模式下，函数在代码块外可见，相当于var声明

```javascript
'use strict'
/*
没有use strict正常输出
有的话报错sayHi is not defined
*/
let phrase = "Hello";

if (true) {
  let user = "John";

  function sayHi() {
    alert(`${phrase}, ${user}`);
  }
}

sayHi();
```

- 无论是否在严格模式下，setTimeOut和setInterval中的函数中的this默认指向global对象
- 严格模式下属性标志writable:false，为属性赋值会报错。非严格模式下，只是操作不成功

### 两种短路操作

- && 返回第一个转boolean为false的原值，如果操作数都是false，返回最后一个操作数的原值
- || 返回第一个转boolean为true的原值，如果操作数都是true，返回最后一个操作数的原值

### 空值合并运算符‘？？’

- 如果第一个参数不是 `null/undefined`，则 `??` 返回第一个参数。否则，返回第二个参数。
- 不能和|| &&一起使用

### 可选链?.

- 如果可选链 `?.` 前面的值为 `undefined` 或者 `null`，它会停止运算并返回 `undefined`。
- ?.()会检查前边部分的函数是否存在，如果存在就调用，如果不存在则停止运算
- ？.[]读取属性
- 可以和delete一起使用
- 只能用来读取，不能用来写入

```javascript
let user={
  admin(){
    console.log('i am admin');
  }
}
let userGuest={};
user.admin?.();
userGuest.admin?.();//无事发生

delete user?.name;//无事发生
```

### typeof function、null

- ECMA-262规定，任何实现内部[[Call]]方法的对象都应该在typeof检测时返回"function",这也是来自于 JavaScript 语言早期的问题。从技术上讲，这种行为是不正确的，但在实际编程中却非常方便。
- `typeof null` 的结果为 `"object".`

### function是对象

- name属性，函数的名称，会根据上下文推断、
- length属性 函数定义时入参的个数，...rest不参与计算
- 可以位函数添加属性，这个新添加的属性对它的执行没有任何影响
- 命名函数表达式 let f=function **f1** (){}
  - 仍然是函数表达式，而不是函数声明
  - 可用于递归
  - 在函数外不可见，也就是在递归调用时，外部通过修改函数变量不会影响到函数内部

```javascript
'use strict'
function f(){
  console.log(123);
}
f=null;
f();//error: f is not a function
```

```javascript
'use strict'
let f1 = function f(){
  console.log(123);
}
f=null;//error f is not defined
```

### this指向

- 在非严格模式下，当一个函数在没有明确（通过成为某个对象的方法，或者通过call()/apply()）指定this值的情况下执行时，this值等于Global对象
- 在严格模式下，this没有指向时，this为undefined
- 箭头函数的this指向函数外层的this

### arguments

- arguments同时时array like和iteabel
- 箭头函数没有arguments,访问到的arguments属于外部函数

### call，apply和bind

- func.call和func.apply 返回func的执行结果
  - call期望的参数与func的形式一致
  - apply期望的参数是类数组，arguments正好是类数组
  - apply内部有优化 会更快
- func.bind 返回类似于函数的特殊对象，就行绑定了this的func.
  - 类函数保存了bind当时的状态，即使func后来被改变了 类函数的执行还是当时的状态

### Math.random()

Math.random()方法返回一个0-1的随机小数，包含0但不包含1.从一组连续整数中随机出一个数：
number = Math.floor(Math.random()*totalnumberofchoices+firstpossiblevalue)

### 目标节点

浏览器总是假定click事件的目标节点，就是点击位置嵌套最深的那个节点

```html
<div id="div"><button id="btn">click</button></div>
```

```javascript
document.getElementById('div').addEventListener('click',function(e){
console.log(e.target);//button
console.log(e.currentTarget);//div
},false);
```

### 在js中使用模块功能前提

- 在你的 HTML 中需要包含 type="module" 的 `<script>` 元素这样的脚本，以便它被识别为模块并正确处理
- 不能通过 file:// URL 运行 JS 模块 — 这将导致 CORS 错误。你需要通过 HTTP 服务器运行。

### 跨域的定义

根据[MDN Web Docs](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS) 里的定义，跨域是指 *当一个资源从与该资源本身所在的**服务器不同的域或端口不同的域或不同的端口**请求一个资源时，资源会发起一个 **跨域 HTTP 请求** 。* 也就是说，正常的跨域情况，是你访问了一个A网站，然后这个网站返回的资源里面，请求了B网站/端口的资源，于是就跨域了。所以，跨域这个情况只会出现在浏览器页面里，因为实际上是浏览器由于安全原因限制了这些请求的访问。

### ==和===

- 处于相等判断符号 `==` 两侧的值会先被转化为数字
- 严格相等运算符 `===` 在进行比较时不会做任何的类型转换。
- `undefined` 和 `null` 在相等性检查 `==` 中不会进行任何的类型转换，它们有自己独立的比较规则，所以除了它们之间互等外，不会等于任何其他的值

### 对象key的顺序

- 整数属性会被进行排序，其他属性则按照创建的顺序显示
- 这里的“整数属性”指的是一个可以在不做任何更改的情况下与一个整数进行相互转换的字符串。

### 构造函数

```javascript
function User(name){
  this.name=name;
}
//使用new调用
function User(name){
  this={};
  this.name=name;
  return this;
}
```

### Object系列方法

- Object.keys(obj) 返回对象所有键的数组
- Object.values(obj) 返回对象所有值的数组
- Object.entries(obj) 返回对象所有键值对 [key,value] 的数组
- 以上几种方法都会忽略Symbol键
- Object.getOwnPropertySymbols(obj) 返回对象所有Symbol类型的键的数组
- Object.fromEntries(iterable) 把键值对列表转换为对象

### instanceof操作符 obj instanceof Class

- 先判断class是否有静态方法Symbol.hasInstance  该方法接受obj作为参数 返回Boolean结果
- 接着检查Class.prototype是否在obj的原型链上

### Object.prototype.toString方法

- 可以通过call/apply/bind来在所有类型的对象上执行
- 返回这个对象的Symbol.toStringTag属性

```javascript
let user={
  [Symbol.toStringTag]:'user'
}
let u = Object.prototype.toString.call(user);
//或者
let u1 = {}.toString.call(user);
console.log(u);//[object user]
console.log(u);//[object user]
```

- js中所有的类型都实现了这个属性
  * 对于 number 类型，结果是 `[object Number]`
  * 对于 boolean 类型，结果是 `[object Boolean]`
  * 对于 `null`：`[object Null]`
  * 对于 `undefined`：`[object Undefined]`
  * 对于数组：`[object Array]`

### typeof {}.toString.call() instanceof

| typeof           | 用于原始数据类型                                         | 返回string |
| ---------------- | -------------------------------------------------------- | ---------- |
| {}.toString.call | 用于原始数据类型，内建类型，有symbol.toStringTag属性类型 | 返回string |
| instanceof       | 对象                                                     | Boolean    |

### for...in循环和Object.keys的区别

```javascript
'use strict'
let user={
  name:'wyj'
}
let admin={
  isAdmin:true,
  __proto__:user
}

console.log(Object.keys(admin));//只有isAdmin
for(let key in admin){
  console.log(key);//会遍历自己的以及继承的key
}
for(let key in admin){
  if(admin.hasOwnProperty(key)){
    console.log(key);//使用obj.hasOwnProperty(key)判断是否是自己的属性
  }
}
```

### 每隔一段时间执行方法的两种方式

```javascript
let delay = 1000;
let timeId=setTimeout(function f(){
  //xxxx
  timeId=setTimeout(f,delay);
  //可以修改delay
  //可以停止
},delay);

let id=setInterval(function(){
  //xxx
  //可以停止
},delay);
```

### 异常处理

#### 异常扩展

```javascript
class MyError extends Error{
  constructor(messsage){
    super(messsage);
    this.name=this.constructor.name;
  }
}

class ValidateError extends MyError{

}

class PropertyRequiredError extends ValidateError{
  constructor(property){
    super(property + ' required');
    this.property=property;
  }
}

try{
  let u=JSON.parse('{"age":18}');
  if(!u.name){
    throw new PropertyRequiredError('name');
  }
}catch(error){
  if(error instanceof ValidateError){
    console.log(error);
    console.log(error.name);
    console.log(error.message);
  }else if(error instanceof SyntaxError){
    console.log("JSON Syntax Error: " + error.message);
  }else{
    throw error;
  }
}
```

#### 全局异常处理

```javascript
window.onerror = function(message, url, line, col, error) {
    alert(`${message}\n At ${line}:${col} of ${url}`);
  };
```

### json

#### json格式

```javascript
let json = `{
    name: "John",                     // 错误：属性名没有双引号
    "surname": 'Smith',               // 错误：值使用的是单引号（必须使用双引号）
    'isAdmin': false                  // 错误：键使用的是单引号（必须使用双引号）
    "birthday": new Date(2000, 2, 3), // 错误：不允许使用 "new"，只能是裸值
    "friends": [0,1,2,3]              // 这个没问题
  }`;
```

#### json系列方法

- JSON.parse(str,(key,value)=>{//处理key value})

```javascript
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';

let meetup = JSON.parse(str, function(key, value) {
  if (key == 'date') return new Date(value);
  return value;
});

alert( meetup.date.getDate() );
```

- JSON.stringify(obj,[...keys]|(key,value)=>{//处理key value},number|string)
- 上边的number是空格的数量，string是用具体的字符串表示缩进
- 循环引用会报错
- 在要被序列化的对象中添加toJson方法，会在序列化时默认调用
  - toJson

### 解构赋值

#### 数组

- 等号右边可以是任意Iterable
- 等号左边是可以被赋值的项

```javascript
let user={};
[user.name,user.age]=['wyj',30];
console.log(user.name);
console.log(user.age);
```

```javascript
let a='a';
let b='b';
[a,b]=[b,a];
console.log(a);//b
console.log(b);//a
```

#### 对象

- 使用 `：`来给属性赋值另一个名字的变量
- 智能函数参数

```javascript
function showMenu(title = "Untitled", width = 200, height = 100, items = []) {
  // ...
}

showMenu(null,null,2000,10);
//上边是难看版本
function showMenu({title = "Untitled", width = 200, height = 100, items = []}={}) {
    // ...
}

showMenu({
    width:900,
    item:[12,3],
})
```

#### 在方法参数中的使用

```javascript
function dosth(arg1,arg2,...rest){}
function dosth1(...rest){}
let arr1=[1,2,3];
let arr2=[3,4,5];
dosth1(...arr1,7,8,9,...arr2);

```

### 对象包装器

#### 出现的原因

- 人们希望可以对原始类型执行操作
- 同时希望原始类型尽量简单

#### 解决方式

- 原始类型任然是原始的，提供单个值
- 在访问其属性和方法时，创建其包装对象，访问包装对象的方法和属性
- 销毁包装对象，只留下原始值

```javascript
let str='abc';//1
str.test=5;//2
console.log(str.test);//3
//非严格模式下undefind,因为在2时，test属性添加后，包装对象就销毁了，在3又创建了另一个包装对象
//在严格模式下会报错：Cannot create property 'test' on string 'abc'
```

### Number类型的精度问题

`` o.1+0.2 == 0.3//false``

#### 出现的原因

- 在内部数字以64位二进制存储，其中52位存储数字，11位标识小数点的位置，1位用于符号 ``1e500//infinity``
- 二进制数字系统中，可以保证以2的整数次幂作为除数正常工作，但其他的除数都变成无限循环小数
- js中的数字通过将数字舍入最接近的数字来解决问题，这些舍入规则是我们丢失了极小的精度

#### 解决方法

```javascript
let sum=0.1+0.2;
console.log(sum.toFixed(2));

let n=6.35;
console.log(n.toFixed(1));//6.3
//出现的原因,如下，四舍五入就变成6.3
console.log(n.toFixed(20));//6.34999999999999964473
//解决方法
console.log(Math.round(6.35*10)/10);
```

### 字符串比较

- 从第一位开始对比相同位置字符UTF-16的编码大小
- 因为不同语言的字母都不相同，因此浏览器需要知道要比较的语言
- 现代标准提供了比较不同语言的字符串的方法 ``str1.localCompare(str2)//返回正数负数或0``

```javascript
let str='AaBbCc3456';
let index;
for(let i=0;i<str.length;i++){
  index=str.codePointAt(i);//返回所在位置编码数字
  console.log(index);
  console.log(String.fromCodePoint(index));//返回编码代表的字符
}
```

### Symbol

- symbol表示唯一的标识符，用**Symbol()来创建值**
- 可以使用**Symbol('xx')** 给symbol一个描述
- symbol创建出来即唯一，与描述无关
- symbol不会被隐式转换为字符串，这是一种防止混乱的“语言保护”，因为字符串和 symbol 有本质上的不同，不应该意外地将它们转换成另一个。如果我们真的想显示一个 symbol，我们需要在它上面调用 `.toString()，或者获取 symbol.description 属性`

#### 作用

- 用来表示对象的属性键值
- 代码的任何其他部分都不能访问或改写这些属性
- symbol属性在for-in循环中、在Object.keys()中不能被访问到
- Object.assign()会同时复制字符串和symbol属性

### Object与原始值的转换

#### string

```javascript
alert(obj); 
anotherObj[obj]
```

#### number

```javascript
Number(obj);
+obj;
date1-date2;
user1>user2;
```

#### default

```javascript
obj1+obj2;//因为字符串和数字都可以应用+
if(user==1){
	//与字符串、数字、symbol进行==比较，这时应该用哪种转换不明显，因此也是用default
}
```

#### 转换时进行的操作

- 如果存在[Symbol.toPrimitive]方法，这调用这个方法完成所有转换
  - 这个方法必须返回一个原始值，否则会报错
- 否则，如果hint是string，尝试调用 `toString`或者 `valueOf方法`
- 否则，如果hint是number或者default，尝试调用 valueOf或者 `toString`

```javascript
let user={
  name:'john',
  age:19,
  [Symbol.toPrimitive](hint){
    console.log(`hint,${hint}`);
    return hint=='string'?this.name:this.age;
  }
}

alert(+user);//hint,number    19
alert(user);//hint,string    john
alert(user+500);//hint,default  519
```

##### toString/valueOf

- string hint 优先调用toString
- 其他hint 优先调用valueOf
- 这些方法必须返回原始值，返回对象会被忽略
- 默认情况下，普通对象已经具有toString和valueOf方法
  - toString返回 "[object Object]"
  - valueOf返回对象自身
- 可以重写这些方法

```javascript
let user={
  name:'wyj',
}

alert(user);//[object Object]
alert(user===user.valueOf());//true
alert(user+2);//二元加法：更愿意接受字符串
```

### Iterator

- 对象有Symbol.iterator的方法
- 当for...of循环启动时，会调用这个方法，如果没有则报错
- 这个方法必须返回一个迭代器，一个有next方法的对象
- for...of循环获取下一个值时，调用对象的next方法
- next方法返回的结果格式为{done:Boolean,value:any}

#### 简单示例

- 第一种

```javascript
let user = {
  from: 1,
  to: 5,
  [Symbol.iterator]() {
    return {
      current: this.from,
      last: this.to,
      next() {
        if (this.current <= this.last) {
          return { done: false, value: this.current++ };
        } else {
          return { done: true };
        }
      },
    };
  },
};

for(let item of user){
    console.log(item);
}
```

- 第二种

```javascript
let user = {
  from: 1,
  to: 5,
  [Symbol.iterator]() {
    this.current=this.from;
    return this;
  },
  next(){
    if(this.current<=this.to){
        return {done:false,value:this.current++};
    }else{
        return {done:true};
    }
  }
};

for(let item of user){
    console.log(item);
}
```

#### 可迭代对象和类数组/iterable&array-like

- array-like是有索引和length属性的对象
- 这两种都可以被Array.from方法转变成数组

### Map和Set

#### 示例代码

```javascript
let map=new Map;
map.set(1,'abc');
map.set('1',1);
map.set(true,'boolean');
console.log(map.get(1));
map.delete(1);
for(let i of map.keys()){
    console.log(i);
}
for(let i of map.values()){
    console.log(i);
}
for(let i of map.entries()){
    console.log(i);
}//for of map默认
let entries = Object.entries({
    a:1,
    b:2,
    c:3,
});
console.log(entries);
/*
    [['a',1],['b',2],['c',3]]
*/
let map2=new Map(entries);
Object.fromEntries(map2);
/*
    [['a',1],['b',2],['c',3]]
*/
```

### WeakMap和WeakSet

#### weak

- 只能用对象作为键(weakmap)和值(weakmap)
- 当这些对象除了作为weakmap的键或weakmap的值，在其他的地方都不可达时，它们所代表的项也会在集合中被自动清除
- 由于集合中项的移除是由垃圾回收器自动执行，所以不能确定weak集合中确定的项
- 不支持clear size keys values和迭代

#### 示例代码

- map和set

```javascript
let m=new Map;
let obj={name:'wyj'};
m.set(obj,1);
obj=null;
console.log(m.size);
/*
结果是1，
因为{name:'wyj'}这个对象被作为m的键引用着
*/
```

- weakmap和weakset

```javascript
class VisitCount{
    visitCountMap=new WeakMap;
    countUser(user){
        let count=this.visitCountMap.get(user)||0;
        this.visitCountMap.set(user,count+1);
    }
}
//在另外的地方调用
let v=new VisitCount();
let user={name:'wyj'};
v.countUser(user);
user=null;//用户离开后，visit自动清空
```

### Date

#### 构造函数

- 无参 创建当前时间的date对象
- new Date(milliseconds) 创建一个 `Date` 对象，其时间等于 1970 年 1 月 1 日 UTC+0 之后经过的毫秒数
  - date.getTime() 或者把date转化为number可以获取毫秒数
  - 创建1970 年 1 月 1 日之前的时间用负数
- new Date(str) 创建一个 Date对象，字符串格式为 'YYYY-MM-DDTHH-mm-ss.sssZ'
  - T是分割符
  - 可选Z是 +-hh:mm代表时区
  - Date.parse(str) 方法有相同的用法
- new Date(year,month,date,hours,minutes,seconds,ms) 创建Date对象
  - 其中前两个参数是必须的
  - month从0开始代表一月
  - date默认是1，h m s ms默认是0

#### 不易记住的方法

- getDate() 返回的日期 从1开始
- getDay() 返回星期几 从0开始代表周日
- set系列方法和构造函数都有自动校准功能
- Date.now() 它相当于 `new Date().getTime()`，但它不会创建中间的 `Date` 对象。因此它更快，而且不会对垃圾回收造成额外的压力

```javascript
let date1 = new Date(2013, 0, 32); // 32 Jan 2013 ?!?
alert(date1); // ……是 1st Feb 2013!

let date2 = new Date(2016, 1, 28);
date3.setDate(date2.getDate() + 2);

alert( date2 ); // 1 Mar 2016

let date3 = new Date();
date3.setSeconds(date3.getSeconds() + 70);

alert( date3 ); // 显示正确的日期信息
```

##### 这月有多少天

```javascript
function getLastDayOfMonth(year, month) {
  let date = new Date(year, month + 1, 0);
  return date.getDate();
}
```

### 词法环境

#### 概念

- 每个运行的函数，代码块以及整个脚本，都有词法环境
- 由两部分组成
  - 环境记录:一个对象，存储所有局部变量、this等其他信息作为其属性
  - 对外部词法变量的引用

#### 函数

- 所有函数在诞生时，都会记住创建它的词法环境
- 所有函数都有一个隐藏的Environment属性，保存了对创建该函数的词法变量的引用

### 数据属性标志和数据属性描述符

#### Object.getOwnPropertyDescriptor

```javascript
let user = {
  name: "John"
};

let descriptor = Object.getOwnPropertyDescriptor(user, 'name');

alert( JSON.stringify(descriptor, null, 2 ) );
/* 属性描述符：
{
  "value": "John",
  "writable": true,
  "enumerable": true,
  "configurable": true
}
*/
```

#### Object.defineProperty(obj,propetyName,descriptor)

- 如果该属性propertyname存在 更新其desc
- 如果不存在创建新的属性
- 没有提供的标志，默认为false
- 通过obj.xxx {xxx:''}这些方式添加的属性，默认标志都是true
  - enumerable:false
    - 不可枚举
    - 不会出现在for..in循环中
    - 不会出现在Object.keys中
  - configurable:false
    - 不可配置
    - 属性不能被删除
    - 属性的标志不能被修改
    - 有一个例外writable可以从ture->false
  - value
  - writable

#### Object.defineProperties(obj,descriptors)

- 允许一次定义多个属性

#### Object.getOwnPropertyDescriptors

- 返回包含symbol类型和不可枚举的属性在内的所有属性描述
- 与Object.defineProperties配合使用可以克隆对象

```javascript
let obj={name:'wyj'};
let clone=Object.defineProperties({},Object.getOwnPropertyDescriptors(obj));
console.log(clone);
```

### 访问器属性

```javascript
let user={
  firstname:'wang',
  lastname:'yj',
  get fullname(){
    return this.firstname+' '+this.lastname;
  },
  set fullname(value){
    [this.firstname,this.lastname]=value.split(' ');
  }
};
```

#### 访问器属性的描述符

- get 函数
- set 函数
- enumerable 访问器属性也在for..in和objct.keys()中
- configurable

```javascript
function User(name,birth){
  this.name=name;
  this.birth=birth;
  Object.defineProperty(this,'age',{
    get(){
      return new Date().getFullYear()-birth.getFullYear();
    }
  })
}

let u=new User('wyj',new Date('1991-6-21'));
console.log(u.age);
```

### 原型继承

- 对象有一个隐藏的属性[[Prototype]],形成原型链
  - 这个属性可以通过 `` __proto__``访问器访问
    - `` __proto__``是对[[Prototype]]属性的访问器属性
    - 现在代码通过 `` Object.getPrototypeOf/Object.setPrototypeOf``两个方法代替 `` __proto__``
- 构造函数有prototype属性 F.prototype
  - 由此构造函数创建的对象的[[Prototype]]属性 指向构造函数的prototype属性
  - F.prototype仅在new F是被瞬时赋值，如果之后修改为新对象，之前new F创建的对象的[[Prototype]]会引用旧对象
  - 构造函数默认有prototype属性，是只有一个constructor属性的对象
    - constructor默认指向构造函数
    - 因此不知道构造函数的时候，要创建类似的对象可以这样

```javascript
function Obj(){}

let obj=new Obj();
let obj2=new obj.constructor();
```

### 类

#### 基本概念

- type class === 'function'
- class不仅仅是语法糖
  - class 函数有特殊的[[IsClassConstructor]]:true属性，这个属性是class函数只能通过new来调用，否则会报错
  - class 的字符串表示都已class开头
  - class中的方法不可枚举，类定义将 `"prototype"` 中的所有方法的 `enumerable` 标志设置为 `false`。
  - class中的字段和属性可以枚举
  - 类内部的代码总是使用'use strict'
  - 字段初始化在构造函数之前

```javascript
class User{
  name='wyj'//2

  constructor(age){
    this.age=age;//3
  }
}
let u =new User(17);//1
for(let key in u){
  console.log(key);//name,age
}
```

#### 类继承

- 继承类除了继承父类的方法 也继承了父类的属性 包括类字段
  - 方法的继承是因为之类的prototype.[[prototype]]指向了父类的prototype
  - 属性的继承是因为 调用了父类的构造函数
- 继承类在new时的执行顺序

```javascript
class User{
  name='wyj'//3

  constructor(age){
    this.age=age;//4
  }
}

class admin extends User{
  isAdmin=true//5
  constructor(age,pwd){
    super(age);//2
    this.pwd=pwd;//6
  }
}

let a=new admin(18,123);//1
```

- 继承类的构造函数的特殊之处

```javascript
class User{
  name='wyj'
  constructor(){
    console.log(this.name);
  }
}

class Admin extends User{
  name='admin'
  constructor(){
    super();
  }
}

let a=new Admin;
//wyj
/*
出现的原因
继承类ctor有[[constructorkind]]:derevied属性，new的时候由父类ctor创建this
类字段 相当于 this.name=xxx 但是在构造函数之前执行
继承类的类字段在super之后执行，所以this.name='wyj'
*/
```

```javascript
class User{
  sayName(){
    console.log('wyj');
  }
  constructor(){
    this.sayName();
  }
}

class Admin extends User{
  sayName(){
    console.log('admin');
  }
  constructor(){
    super();
  }
}

let a=new Admin;
//admin
/*
出现的原因
继承类ctor有[[constructorkind]]:derevied属性，new的时候由父类ctor创建this
对象或者类方法中有[[homeobject]]指向该对象或者new出来的对象
[[homeobject]]仅被用于super调用，可以解析原型及其方法
*/

class Animal{
  name='animal'
  eat(){
    console.log(this.name+' eats');
  }
}

class Rabbit extends Animal{
  name='rabbit'
  eat(){
    super.eat();
  }
}

let r=new Rabbit;
r.eat();//rabbit eats
```

#### 静态属性和静态方法

- 静态方法中的this是类本身 可以在静态方法中使用new this()来构建对象
- extends 子类继承了父类的静态属性和方法。子类(其实是function对象)的[[prototype]]指向了父类
- 内建类没有使用extends继承 因此内建类上没有Object.xxx系类方法

### Promise

#### 对象构造器语法

```javascript
let promise=new Promise(function(resolve,reject){
	//执行的代码
})
```

- 传递给promise构造器的函数叫做executor
- 当promise被创建出来时，executor自动执行
- 当executor执行出了结果，它应该调用两种回调之一
  - resolve(value)
  - reject(error)
- promise的内部属性，我们无法直接访问
  - state
    - 最初是pending
    - 在resolve被调用后变为fullfilled
    - 或者在reject被调用后变为rejected
  - result
    - 最初是undefined
    - 在resolve被调用后变为value
    - 或者在reject被调用后变为error
- promise只能有一个结果，在executor中执行了resolve或者reject，之后的代码会被忽略

![1666078382455](image/js/1666078382455.png)

#### 消费者

- then(function(result){},function(error){})
  - 返回一个新的promise
  - ![1666131686271](image/js/1666131686271.png)
- catch是对.then(null,function(error){})的模拟
  - 返回一个新的promise
  - 与上边then一致
- finally
  - 处理程序没有参数
  - 返回结果会被忽略
  - 抛出error，执行将转到最近的error处理程序

#### 错误处理

- promise的executor和then的handler都有隐形的try..catch

```javascript
new Promise((resolve,reject)=>{
    throw new Error();
})
//相当于
new Promise((resolve,reject)=>{
    reject(new Error);
})
```

- then方法可以把未处理的reject promise传递下去

```javascript
let p=new Promise((resolve,reject)=>{
    reject(new Error('123'));
});
p.name='p';
console.log(p);//reject promise
let t= p.then();
console.log(t);//another rejected promise
let t2=t.catch(e=>{
    console.log(e);//直到处理程序的出现
})
console.log(t2);//处理了后 fulfilled promise

```

- 浏览器可以添加*unhandledrejection*方法处理未处理的rejection

```javascript
window.addEventListener('unhandledrejection', function(event) {
    // 这个事件对象有两个特殊的属性：
    console.log(event.promise); // [object Promise] —— 生成该全局 error 的 promise
    console.log(event.reason); // Error: Whoops! —— 未处理的 error 对象
  });
  
 let p= new Promise(function() {
    throw new Error("Whoops!");
  }); // 没有用来处理 error 的 catch
  p.name='p';
```

#### Promise静态方法

##### Promise.all

- Promise.all(iterable) 期待一个promise数组，返回一个promise
- then 所有promise都fullfilled了之后，then(handler)接收到resolve数组
- 结果数组的顺序与promise数组一致
- 任意一个promise reject，返回的promise立即reject 并且带有这个error

##### Promise.allSettled

- Promise.allSettled(iterable) 期待一个promise数组，返回一个promise
- then 所有promise都settled之后，无论结果如何，返回一个数组
  - {status:'fulfilled',value:result}
  - {status:'rejected',reason:error}
- 结果数组的顺序与promise数组一致
- 用Promise.all模拟

```javascript
if(!Promise.allSettled){
  Promise.allSettled=function(promises){
    let all = promises.map(p=>p.then(function(result){
      return {status:'fullfilled',value:result};
    },function(error){
      return {status:'rejected',reason:error};
    }));
    return Promise.all(all);
  }
}
```

##### Promise.race

与Promise.all类似，但只等待第一个settled的promise，并获取其结果result或者error

##### Promise.any

- 与Promise.race类似，但只等待第一个 fulfilled 的 promise，并获取其结果
- 如果所有的promise都rejected，则返回一个rejected promise，error位AggregateError，它有一个数组包含所有的error

```javascript
Promise.any([
  new Promise((resolve, reject) => setTimeout(() => reject(new Error("Ouch!")), 1000)),
  new Promise((resolve, reject) => setTimeout(() => reject(new Error("Error!")), 2000))
]).catch(error => {
  console.log(error.constructor.name); // AggregateError
  console.log(error.errors[0]); // Error: Ouch!
  console.log(error.errors[1]); // Error: Error!
});
```

##### Promise.resolve/reject

#### Promisify

把类似下边的函数转化成promise版本

```javascript
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`Script load error for ${src}`));

  document.head.append(script);
}

loadScript('src',function(error,script){

});
```

首先是简单转换

```javascript
function promiseLoadScript(src){
  return new Promise((resolve,reject)=>{
    loadScript(src,(error,script)=>{
      if(error){
        reject(error);
      }
      resolve(script);
    })
  })
}
```

通用版本

```javascript
function promisify(f){
  return function(...args){
    return new Promise((resolve,reject)=>{
      function callback(err,result){
        if(err){
          reject(err);
        }else{
          resolve(result);
        }
      }
      args.push(callback);
      f.call(this,...args);
    })
  }
}
```

#### 微任务队列

promise.then/catch/finally 都是异步的，会被放到微任务队列中执行

只有在JavaScript引擎没有其他任务在运行时才会执行微任务队列

```javascript
new Promise(resolve=>{
  console.log(1);
  resolve()
}).then(v=>{
  console.log(4);
}).then(v=>{
  console.log(6);
});
new Promise(resolve=>{
  console.log(2);
  resolve()
}).then(v=>{
  console.log(5);
}).then(v=>{
  console.log(7);
});
console.log(3);
```
