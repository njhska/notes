### let 和 var

- let声明的范围是块作用域，而var声明的范围是函数作用域
- JavaScript引擎会记录用于变量声明的标识符及其所在的块作用域，因此嵌套使用相同的标识符不会报错，即变量名+块id 确定一个变量
- var关键字不同，使用let在全局作用域中声明的变量不会成为window对象的属性，不过，let声明仍然是在全局作用域中发生的，相应变量会在页面的生命周期内存续。

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

### this指向

- 在非严格模式下，当一个函数在没有明确（通过成为某个对象的方法，或者通过call()/apply()）指定this值的情况下执行时，this值等于Global对象
- 在严格模式下，this没有指向时，this为undefined
- 箭头函数的this指向函数外层的this

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

### 函数声明

- 在严格模式下，函数声明在代码块内时，它在代码块内的任意位置可见，但在代码块外不可见

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
