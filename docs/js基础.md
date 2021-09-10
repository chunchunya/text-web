# js的基本数据类型

Undefined,Null,String,Number,Boolean,Bigint,Symbol

# JavaScript数据类型判断

确定一个值是哪种基本类型可以使用` typeof`操作符，而确定一个值是哪种引用类型可以使用`instanceof `操作符。

注意：

 1：为什么 `typeof null`的返回值也是 `"object"`呢？因为 null 代表的是**空对象**。
		 2：`typeof NaN`的返回值是 `"number"`。**NaN**是一个特殊的数字，表示Not a Number，非数值。NaN 是一个特殊的数字。Undefined和任何数值计算的结果为 NaN。NaN 与任何值都不相等，包括 NaN 本身。
         3： `typeof []`、 `typeof {}` ，返回值也是 `object` 。因为这里的返回结果`object`指的是**引用数据类型**。空数组、空对象都是**引用数据类型 Object**。

# this的指向问题

直接使用函数，this指向全局。对象调用函数，this指向对象。

![image-20210729183609970](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729183609970.png)

**阿里的一道笔试题**

```javascript
var name =222;
var a = {
    name: 111,
    say: function(){
        console.log(this.name)
    }
}
var fun = a.say;
fun();   //fun.call(window)  === 222
a.say();  //a.say.call(a)   === 111


var b = {
    name: 123,
    say: function(fun){
        fun();
    }
}
b.say(a.say);    //  等同于fun = a.say; fun()   fun.call(window)   ===222
b.say = a.say;
b.say();         // b.say.call(b) === 123
```

　this永远指向的是最后调用它的对象，也就是看它**执行**的时候是谁调用的，上式中虽然函数say是被对象a所引用，但是在将say赋值给变量fun的时候并没有执行,所以最终指向的是window，这和下一行是不一样的，a.say()是直接执行了say函数。

**箭头函数**中本身没有this，导致它会从上一级代码块中寻找this，继承自父执行上下文中的this。

![image-20210729183633968](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729183633968.png)

 this 没有作用域的限制，这点和变量不一样，所以嵌套函数不会从调用它的函数中继承 this，这样会造成很多不符合直觉的代码。要解决这个问题，你可以有两种思路：

- 第一种是把 this 保存为一个 self 变量，再利用变量的作用域机制传递给嵌套函数。
- 第二种是继续使用 this，但是要把嵌套函数改为箭头函数，因为箭头函数没有自己的执行上下文，所以它会继承调用函数中的 this

例如：

```javascript
var myObj = {
  name : " 极客时间 ", 
  showThis: function(){
    console.log(this)
    function bar(){console.log(this)}   
    bar()
  }
}
myObj.showThis()
```

执行这段代码后，你会发现函数 bar 中的 this 指向的是全局 window 对象，而函数 showThis 中的 this 指向的是 myObj 对象。

可以通过一个小技巧来解决这个问题，比如在 showThis 函数中声明一个变量 self 用来保存 this，然后在 bar 函数中使用 self，代码如下所示：

```js
var myObj = {
  name : " 极客时间 ", 
  showThis: function(){
    console.log(this)
    var self = this
    function bar(){
      self.name = " 极客邦 "
    }
    bar()
  }
}
myObj.showThis()
console.log(myObj.name)
console.log(window.name)
```

执行这段代码，可以看到它输出了我们想要的结果，最终 myObj 中的 name 属性值变成了“极客邦”。其实，这个方法的的本质是把 this 体系转换为了作用域的体系。

其实，也可以使用 ES6 中的箭头函数来解决这个问题，结合下面代码：

```js
var myObj = {
  name : " 极客时间 ", 
  showThis: function(){
    console.log(this)
    var bar = ()=>{
      this.name = " 极客邦 "
      console.log(this)
    }
    bar()
  }
}
myObj.showThis()
console.log(myObj.name)
console.log(window.name)
```

执行这段代码，会发现它也输出了我们想要的结果，也就是箭头函数 bar 里面的 this 是指向 myObj 对象的。这是因为 ES6 中的箭头函数并不会创建其自身的执行上下文，所以箭头函数中的 this 取决于它的外部函数。



# 深拷贝浅拷贝

**浅拷贝**是创建一个新对象，这个对象有着原始对象属性的一份精确拷贝。**如果属性是基本类型，拷贝的就是基本类型的值**，如果属性是引用类型，拷贝的就是内存地址，所以其中一个对象改变了这个地址，就会影响到另一个对象。（浅拷贝，旧对象的属性都拷给新对象了，当属性值是基本类型数据时，如果新对象改变此属性值，则不影响旧对象，当属性值时引用类型数据时，由于拷贝的时内存地址，所以两个对象指向相同的地址，会互相影响）

**深拷贝**是将一个对象从内存中完整地拷贝一份出来，从堆内存中开辟一个新的区域存放新对象，且修改新对象不影响原对象。

## 针对引用类型来说，赋值、深拷贝、浅拷贝的区别

1.浅拷贝、赋值的区别

**赋值：**当我们把一个对象**赋值**给一个新的变量时，赋得其实是该对象在栈中的地址，而不是堆中的数据，也就是两个对象指向的是同一个存储空间，无论哪个对象发生改变，其实都是改变的存储空间的内容，因此两个对象是联动的。

**浅拷贝：**重新在堆中创建内存，拷贝前后对象的基本类型互不影响，但拷贝前后对象的引用类型因共享一块内存，会相互影响。

**深拷贝：**从堆内存中开辟一个新的区域存放对象，对对象中的子对象进行递归拷贝，拷贝前后的两个对象互不影响。

![image-20210729183645687](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729183645687.png)

深、浅拷贝实现方式：

![image-20210729183716328](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729183716328.png)

**注意：**函数、日期、正则格式的数据用JSON.parse(JSON.stringify())方式进行深拷贝，会变成空对象，会把undefined去掉。（用此方法进行深拷贝的弊端）

```javascript
//深拷贝1
let deepCopy = (newObj,oldObj)=>{
    for(let key in oldObj){
        let item= oldObj[key];
        if(item instanceof Array){
            newObj[key] = [];
            deepCopy(newObj[key],item);

        }else if(item instanceof Object){
            newObj[key] = {};
            deepCopy(newObj[key],item);
        }else{
            newObj[key] = item;
        }
    }
    return newObj;
}

//深拷贝2的写法
let deepCopy2 = (obj)=>{
    let copyObj = {};
    if(obj === null) return obj;    //null也是一个空对象
    if(obj instanceof Date) return new Date(obj);   //判断日期和正则
    if(obj instanceof RegExp) return RegExp(obj);
    if(typeof obj !== 'object') return obj;
    for(let key in obj){
        if(obj.hasOwnProperty(key)){
            copyObj[key] = deepCopy2(obj[key]);
        }

    }
    return copyObj;
}

//深拷贝还可以用JSON.parse(JSON.stringify(obj))  但是缺点是函数不拷贝，正则也不拷贝

//浅拷贝
let shallowCopy = (oldObj)=>{
    let newObj = {};
    for(let i in oldObj){
        if(oldObj.hasOwnProperty(i)){
            newObj[i] = oldObj[i];
        }

    }
    return newObj;
}

var obj1 = {
    name: '你的名字',
    age: 30,
    hobby:{
        '体育':'打篮球',
        '学习':'看书',
        '生活':'逛街'
    }
}

var obj2 = shallowCopy(obj1);

var obj3 = deepCopy({},obj1);
var obj4 = deepCopy2(obj1)

obj2.name = "变成obj2的名字";
obj2.hobby['体育']= 'obj2打羽毛球';


obj3.name = "变成obj3的名字";
obj3.hobby['体育'] = 'obj3打羽毛球';

obj4.name = "变成obj4的名字";
obj4.hobby['体育'] = 'obj4打羽毛球';

console.log(obj1);       //对于obj2的浅拷贝，name没有变，还是原来的"你的名字"  对象中的"体育"属性变了，变成了和obj2改的一样的   对于obj3的深拷贝，都没有变，两者互不影响
console.log(obj2);
console.log(obj3);
console.log(obj4);
```

# js的作用域

作用域说明:一般理解**指一个变量的作用范围**

1、全局作用域

全局作用域在页面打开时被创建,页面关闭时被销毁

编写在script标签中的变量和函数,作用域为全局，在页面的任意位置都可以访问到

在全局作用域中有全局对象window,代表一个浏览器窗口,由浏览器创建,可以直接调用

全局作用域中声明的变量和函数会作为window对象的属性和方法保存

2 、函数作用域

- 调用函数时,函数作用域被创建,函数执行完毕,函数作用域被销毁
- 每调用一次函数就会创建一个新的函数作用域,他们之间是相互独立的
- 在函数作用域中可以访问到全局作用域的变量,在函数外无法访问到函数作用域内的变量
- 在函数作用域中访问变量、函数时,会先在自身作用域中寻找,若没有找到,则会到函数的上一级作用域中寻找,一直到全局作用域

3、块级作用域（ES6）

## **作用域的深层次理解**

**执行期的上下文**
	    当函数代码执行的前期会创建一个执行期上下文的内部对象A0(作用域)

这个内部的对象是预编译的时候创建出来的因为当函数被调用的时候会先进行预编译

在全局代码执行的前期会创建一个执行期的上下文的对象GO

## 函数作用域预编译

1. 创建ao对象 A0{}
2. 找形参和变量声明  将变量和形参名当做AO对象的属性名    值为undefined
3. 实参形参相统一
4. 在函数体里面找函数声明   值赋予函数体

## 全局作用域的预编译

1. 创建GO对象
2. 找变量声明  将变量名作为GO对象的属性名    值是undefined
3. 找函数声明  值赋予函改体

![image-20210729183843515](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729183843515.png)

预编译完进行解释执行操作。

# 闭包（还是有些不太理解）

简单理解闭包就是函数里面return函数。

在 JavaScript 中，根据词法作用域的规则，内部函数总是可以访问其外部函数中声明的变量，当通过调用一个外部函数返回一个内部函数后，即使该外部函数已经执行结束了，但是内部函数引用外部函数的变量依然保存在内存中，我们就把这些变量的集合称为闭包。

**总结：指有权访问另一个函数作用域中变量的函数。闭包可以让你从内部函数访问外部函数作用域。**

# 防抖节流(待补充)

**防抖：**当持续触发事件（比如键盘一直输入内容），一定时间内没有再触发事件（键盘抬起了并且满足在一定时间内没有再按下去），事件处理函数才会执行一次。

肯定是定时器。在一段时间内，如果又触发了事件，重新开始延时，代表的就是重新开始定时器。

**防抖实际应用**如下：

![image-20210729183932989](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729183932989.png)

**节流：**当持续触发事件的时候，保证一定时间内，只调用一次事件处理函数，一段时间内只做一件事情（每隔一段时间执行一次）。

节流实际应用：鼠标滑动、拖拽

**防抖**和**节流**本质是不一样的，防抖动是将多次执行变为最后一次执行，节流是将多次执行变成每隔一段时间执行。

防抖代码：（输入框一秒内不管输入多少数据，都是一秒后才打印出来）

![image-20210729183943221](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729183943221.png)

![image-20210729183954587](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729183954587.png)

# apply、call、bind的用法和区别

call、apply、bind都是改变this指向的方法。（call和apply非常相似，只是传参方式不同）

- apply 、 call 、bind 三者都是用来改变函数的this对象的指向的；

- apply 、 call 、bind 三者第一个参数都是this要指向的对象，也就是想指定的上下文；

- apply 、 call 两者都可以利用后续参数传参； 但是传参的方式不一样，apply是数组，call是正常传参形式

- bind 返回this指向已经变化的函数，需要手动调用；apply 、call 则是在指向变化后立即调用 。

  比如下面的这个例子。

  一个叫喵喵的猫喜欢吃鱼，一个叫汪汪的小狗喜欢啃骨头，有一天，小狗汪汪和喵喵共进午餐的时候，汪汪说自己想尝尝小鱼干的味道，但是因为有刺，喵喵就想了个办法，说自己先吃，完了喂给汪汪。

![image-20210729184055102](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729184055102.png)

这样，汪汪就吃到了美味的鱼干。可是汪汪每顿都想来点小鱼干，喵喵还要工作去捉老鼠，所以它们又想了一个办法，喵喵把吃鱼的方法教给汪汪。这样，每次汪汪就可以自己吃小鱼干了。

![image-20210729184120361](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729184120361.png)

bind()方法注意：bind的时候传的参数会预先传给返回的方法，调用方法时就不用再传参数了。

如果call()和apply()的第一个参数是null或者undefined，那么this的指向就是全局变量，在浏览器里就是window对象。

![image-20210729184045201](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729184045201.png)

**apply与call在使用方式上的区别**还使得apply有些特殊的用法。比如：如何使用push方法，**不用循环完成两个数组的合并**。

```javascript
var arr1 = [ 1, 2, 3 ]
var arr2 = [ 4, 5, 6 ]
 
Array.prototype.push.apply(arr1, arr2)  // 6 push方法的返回值是合并后数组的长度
console.log(arr1) // [ 1, 2, 3, 4, 5, 6]

```

因为apply的第二个参数可以直接接受数组，因此在不确定具体有多少参数的时候，推荐使用apply方法。

一个综合的例子：

```javascript
function Cat(name){
    this.name = name
    this.sayHi = function(age, favor){
       console.log("hi，I am " + this.name + "," + age + " years old,I like " + favor) 
    }
}
 
function Mouse(name){
    this.name = name
}
 
var cat = new Cat("Tom")
cat.sayHi(12, "fish")   // hi，I am Tom,12 years old,I like fish
var mouse = new Mouse("Jerry", 8, "cheese")
mouse.sayHI(10, "cheese")  // Uncaught TypeError: mouse.sayHI is not a function
 
cat.sayHi.apply(mouse, [10, "cheese"])  // hi，I am Jerry,10 years old,I like cheese
cat.sayHi.call(mouse, 10, "cheese")  // hi，I am Jerry,10 years old,I like cheese
cat.sayHi.bind(mouse, 10, "cheese")()  // hi，I am Jerry,10 years old,I like cheese
```

区别在最后一行 cat.sayHi.bind(mouse, 10, "cheese")()  。可以看到与apply和call相比，在bind()方法后还有个(),这正说明了bind方法返回的是一个函数，并不执行，需要手动去调用才会执行。其返回结果与前两者相同。

注：

```javascript
let arr = Array.prototype.slice.call(伪数组)//可以将伪数组转化为z真正的数组
```

## 使用apply实现获取数组的最大最小值

```js
//使用apply实现获取数组的最大最小值
//因为Math.max()函数只能传入一组参数来求最大值，所以如果是要用于求一个数组中的最大值时，可以用Math.max.apply(Math,array),把this值指向Math对象，则第二个参数可以传入任意数组。
let mmm = [4,2,8,5,3,9,5,1];
console.log(Math.max.apply(Math,mmm));
console.log(Math.min.apply(Math,mmm));
```

# null，undefined 的区别

```
 null 		表示一个对象是“没有值”的值，也就是值为“空”；
 undefined 	表示一个变量声明了没有初始化(赋值)；

 undefined不是一个有效的JSON，而null是；
 undefined的类型(typeof)是undefined；
 null的类型(typeof)是object；

 Javascript将未赋值的变量默认值设为undefined；
 Javascript从来不会将变量设为null。它是用来让程序员表明某个用var声明的变量时没有值的。
注意：
 	在验证null时，一定要使用　=== ，因为 == 无法分别 null 和　undefined
 	null == undefined // true
 	null === undefined // false
```

# js 的事件循环机制

由于JS是单线程的，为了防止一个函数执行时间过长阻塞后面的代码，引入事件循环机制。所以会先将同步任务压入执行栈中，依次执行，将异步任务推入异步队列，异步队列又分为宏任务队列和微任务队列，因为宏任务队列的执行时间较长，所以微任务队列要优先于宏任务队列（按照代码的层级,同层级先执行微任务其次执行宏任务）。

微任务队列的代表就是**Promise.then，MutationObserver，Process.nextTick（Node.js环境下）**等。宏任务的话就是**setImmediate（Node.js环境下） ，setTimeout ，setInterval， I/O ，UI rendering**等。

```js
console.log(1)
process.nextTick(() => {
    console.log(8)
    setTimeout(() => {
        console.log(9)
    })
})
setTimeout(() => {
    console.log(2)
    new Promise(() => {
        console.log(11)
    })
})
let promise = new Promise((resolve,reject) => {
    setTimeout(() => {
        console.log(10)
    })
    resolve()
    console.log(4)
})
fn()
console.log(3)
promise.then(() => {
    console.log(12)
})
function fn(){
    console.log(6)
}
//依次输出 1，4，6，3，8，12，2，11，10，9
```

注意：promise后面then前面的还是同步任务。

# 设计模式

**1.单例模式的理解**

定义：1.只有一个实例  2.可以全局访问

主要解决：一个全局使用的类，频繁地创建和销毁

何时使用：当你想控制实例的数目，节省系统化资源的时候

如何实现：判断系统是否已经有这个单例 如果有则返回 没有则创建

单例模式优点：内存中只有一个实例 减少了内存的开销 尤其是频繁地创建和销毁实例 （比如说是首页页面的缓存）

使用场景： 1.全局的缓存 2.弹窗

单例模式在创建弹框的应用（es5实现）：

![image-20210729184015162](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729184015162.png)

(es6实现)

![image-20210729184023689](../source/images/js%E5%9F%BA%E7%A1%80/image-20210729184023689.png)

**2.策略模式**

**3.发布订阅模式**

# 立即执行函数

所谓立即执行函数即函数定义完后立即执行。

```javascript
function fn(){}()  //错误的，解析器会理解成一个函数定义，后面不应该有括号，所以就是错的。应该用（）包起来

(function(){}()) //正确！

(function(){})() //正确！
```

立即执行函数会形成一个单独的作用域，我们可以封装一些临时变量或者局部变量，避免污染全局变量。

# 遍历数组的方式



# 判断数组的方法

1、`instanceof`

```js
    console.log([] instanceof Array); //  true
```

2、`Array.isArray()`

```js
    console.log(Array.isArray([])); // true    
```

3、`Object.prototype.toString.call()`

```js
    function isArray(arr) {
    return Object.prototype.toString.call(arr) === '[object Array]';
  }
    console.log(isArray([])); // true
```

4、**constructor**判断: 实例的构造函数属性constructor指向构造函数

```js
	let a = [1,3,4]; 
	a.constructor === Array; //true
```

# 数组的一些方法

## reduce方法

```javascript
(1)reduce(function(prev,cur,index,arr){...},init) //有初始值   即当有初始值的时候，prev代表的是init，cur代表的是数组的第一项

(2)reduce(function(prev,cur,index,arr){...})  //无初始值  即当没有初始值的时候，prev代表的是数组的第一项，cur代表的是数组的第二项
```

arr: 表示原数组；

prev: 表示上一次调用回调时的返回值，或者初始值init；

cur: 表示当前正在处理的数组元素；

index: 表示当前正在处理的数组元素的索引，若提供Init值，则索引为0，否则索引为1；

init：表示初始值。

**应用实例**：

```javascript
//数组求和  求积
let arr = [1,2,3,4];
let sum = arr.reduce(function(prev,cur){return prev+cur});
console.log(sum);

//计算数组中每个元素出现的次数
let person = ['张鱼鱼','郑威威','樟醇醇','樟醇醇','郑薇薇','郑威威','鲤鱼','哇哇'];
let count = person.reduce((prev,cur)=>{
    if(cur in prev) {
        prev[cur]++;
    }else{
        prev[cur] = 1;
    }
    return prev;
},{})
console.log(count);

//实现数组去重
let arr2 = [1,2,3,3,4,4,4,5,6,7,8,8,9];
let newArr1 = (arr)=>{
    return arr.reduce((prev,cur)=>{
        if(prev.includes(cur)){
            return prev;
        }else{
            prev.push(cur)
            // prev = prev.concat(cur);
        }
        return prev
    },[])
}
console.log(newArr1(arr2));

//reduce实现数组的扁平化  下一标题提供代码

```

## map方法

map() 方法返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。map()不会对空数组进行检测、不会改变原始数组 。

```javascript
let arr = [1,2,3,4,5,6,7];

//map方法
let newArr = arr.map((item,index)=>{return item+index})
// let newArr = arr.map(function(item,index){return item+index})
console.log(newArr);      //[1,3,5,7,9,11,13]
```

## filter方法

 filter用于对数组进行过滤。 它创建一个新数组,新数组中的元素是通过检查指定数组中符合条件的所有元素。 

**注意:**filter()不会对空数组进行检测、不会改变原始数组 。

```javascript
//filter方法
let newArr1 = arr.filter(function(item,index){
    return item>5&&index>5
})
console.log(newArr1);  //[7]
```

## some方法

some() 方法用于检测数组中的元素是否满足指定条件（函数提供）。会依次执行数组的每个元素：

- 如果有一个元素满足条件，则表达式返回*true*   , 剩余的元素不会再执行检测。
- 如果没有满足条件的元素，则返回false。

**注意：** some() 不会对空数组进行检测，不会改变原始数组。

```javascript
//some方法
let obj1 = [
    {color:'red', fruits:'apple'},
    {color:'yellow', fruits:'orange'}
    ]
let some = obj1.some(function(item,index){
    return item.color = 'red';
})
console.log(some);     //true
```

## every方法

every() 方法用于检测数组所有元素是否都符合指定条件（通过函数提供）。使用指定函数检测数组中的所有元素：

- 如果数组中检测到有一个元素不满足，则整个表达式返回 *false* ，且剩余的元素不会再进行检测。
- 如果所有元素都满足条件，则返回 true。

**注意：** every() 不会对空数组进行检测，不会改变原始数组。

```javascript
//every方法
let obj1 = [
    {color:'red', fruits:'apple'},
    {color:'yellow', fruits:'orange'}
    ]
let every = obj1.every(function(item,index){
    return item.color = 'red';
})
console.log(every);   //false
```

# forEach()方法

和**map方法**类似，唯一的**区别**就是forEach方法没有返回值，是直接在数组本身上进行修改，而map方法是生成一个改后的新数组。

# 数组扁平化处理

**方法一： 使用flat()方法**

​                 注：我运行的时候会报错，提示arr的属性没有这个方法（不推荐）

```javascript
let arr = [1,[2,3,4,5,[6,7,[8]]],9];
let res = arr.flat(Infinity);
console.log(arr);
```

**方法二：正则方法**

```javascript
let arr = [1,[2,3,4,5,[6,7,[8]]],9];
let arr1 = JSON.stringify(arr).replace(/\[|\]/g,""); //将左右括号改成空
console.log(arr1);   //1,2,3,4,5,6,7,8,9
let arr2 = JSON.parse('['+ arr1 +']');
console.log(arr2);
```

**方法三：递归方法**

```javascript
let newArr = [];
function squeeze(arr){
    for(let i=0;i<arr.length;i++){
        if(Array.isArray(arr[i])){  //if(arr[i] instanceof Array)
            squeeze(arr[i]);
        }else{
            newArr.push(arr[i]);   //这里必须写成else形式
        }
    }
    return newArr
}
let arr3 = squeeze(arr);
console.log(arr3);
```

**方法四：reduce+递归方法**

```javascript
//reduce实现数组的扁平化
let arr1 = [1,2,[3,4,5,[6,7,[8]]],9];
let newArr = (arr)=>{
    return arr.reduce(function(prev,cur){
        return prev.concat((Array.isArray(cur))?newArr(cur):cur)
    },[])
}
console.log(newArr(arr1));
```

# 原型

每个函数都会创建一个prototype属性，这个属性是一个对象，这个对象就是通过调用构造函数创建的对象的原型。

**使用原型的好处**就是，在它上面定义的属性和方法可以被构造函数创建的所有对象实例共享。

# 原型链

每个对象实例都会在其内部初始化一个属性，就是prototype(原型)，当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么他就会去prototype里找这个属性，这个prototype又会有自己的prototype（可能是另一个构造函数的实例对象），于是就这样一直找下去，也就是我们平时所说的原型链的概念。

```javascript
function Person() {

}
var person1 = new Person();
var person2 = new Person();
console.log(person1.__proto__ == Person.prototype) // true
console.log(Person.prototype.constructor == Person) // true
console.log(person1.__proto__ ==== person2.__proto__)  //true   同一个构造函数创建的两个实例共享同一个原型对象。

//是否包含指定构造函数的原型
console.log(person1 instanceof Person);   //true
console.log(person1 instanceof Object);   //true
console.log(Person.prototype instanceof Onject);   //true

//注意并不是所有实现都能够对外暴露__proto__，可以使用isPrototypeOf()方法确定两个实例对象与原型对象的关系

console.log(Person.prototype.isPrototypeOf(person1));  //true
console.log(Person.prototype.isPrototypeOf(person2));   //true

// 顺便学习一个ES5的方法,可以获得对象的原型
console.log(Object.getPrototypeOf(person1) === Person.prototype) // true

//hasOwnProperty()方法用于确定某个属性是在实例上还是在原型对象上，来自自身的对象上则会返回true，如果来自原型对象上，则会返回false


//想要列出某对象的所有实例属性,无论是否可以枚举
let keys = Object.getOwnPropertyNames(person1);  
//想要列出某对象上的可以枚举的实例属性
let keys = Object.keys(person1);
```

**原型链的弊端**是：

1.当两个实例对象指向同一个原型的时候，改变其中一个实例对象中的属性，另一个实例对象也会跟着改变。

2.并没有实现super功能（对父类传参）。

# instanceof原理

**instanceof**用来判断构造函数的 prototype 属性是否在对象原型链上。

换句话说：

①用于判断某个实例是否属于某构造函数

②在继承关系中用来判断一个实例是否属于它的父类型或者祖先类型的实例。

### 手写instanceof

```javascript
let baseType = ['undefined','null','string','number','boolean','bigInt','symbol']
// 变量R的原型 存在于 变量L的原型链上
let instance_of = (L,R)=>{
    // 验证如果为基本数据类型，就直接返回false
    if(baseType.includes(typeof(L))){
        return false;
    }
    let RP = R.prototype;    // 取 R 的显示原型
    L = L.__proto__;         // 取 L 的隐式原型
    while(true){             // 无线循环的写法（也可以使 for(;;) ）
        if(L === null){      // 找到最顶层
            return false;
        }
        if(L === RP){        // 严格相等
            return true;
        }
        L = L.__proto__;     // 没找到继续向上一层原型链查找
    }
}
```

# 判断对象是哪个类的直接实例

**问题：已知A继承了B，B继承了C。怎么判断 a 是由A直接生成**的实例，还是B直接生成的实例呢？还是C直接生成的实例呢？

分析：这就要用到原型的`constructor`属性了。

- `foo.__proto__.constructor === Foo`的结果为true，但是 `foo.__proto__.constructor === Object`的结果为false。

所以，**用 consturctor判断就比用 instanceof判断，更为严谨**。

# 继承

## 原型链继承

```javascript
function Parent () {
    this.name = 'kevin';
}
Parent.prototype.getName = function () {
    console.log(this.name);
}
function Child () {
    
}
Child.prototype = new Parent();
var child1 = new Child();
console.log(child1.getName()) // kevin
```

运用原型链继承的**缺点**：引用类型的属性被所有实例共享。比如：

```javascript
function Parent () {
    this.names = ['kevin', 'daisy'];
}
function Child () {

}
Child.prototype = new Parent();
var child1 = new Child();
child1.names.push('yayu');
console.log(child1.names); // ["kevin", "daisy", "yayu"]
var child2 = new Child();
console.log(child2.names); // ["kevin", "daisy", "yayu"]
```

## 盗用构造函数（经典继承）

```javascript
function Parent () {
    this.names = ['kevin', 'daisy'];
}
function Child () {
    Parent.call(this);
}
var child1 = new Child();
child1.names.push('yayu');
console.log(child1.names); // ["kevin", "daisy", "yayu"]
var child2 = new Child();
console.log(child2.names); // ["kevin", "daisy"]
```

**优点：**

1.避免了引用类型的属性被所有实例共享

2.可以在 Child 中向 Parent 传参,比如：

```javascript
function Parent (name) {
    this.name = name;
}
function Child (name) {
    Parent.call(this, name);
}
var child1 = new Child('kevin');
console.log(child1.name); // kevin
var child2 = new Child('daisy');
console.log(child2.name); // daisy
```

缺点：1.必须在构造函数中定义方法，因此函数不能重用

​			2.子类也不能访问父类原型上定义的方法，因此所有类型只能使用构造函数模式。

## 组合继承

结合了原型链和盗用构造函数的优点。基本思路是使用原型链继承原型上的属性和方法（实现了函数复用），而通过盗用构造函数继承实例属性(实现了引用类型的私有化和参数传递)。**这样既可以把方法定义在原型上以实现重用，又可以让每个实例都有自己的属性。**是javascript中使用最多的继承模式。

```javascript
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {

    Parent.call(this, name);
    
    this.age = age;

}

Child.prototype = new Parent();

var child1 = new Child('kevin', '18');

child1.colors.push('black');

console.log(child1.name); // kevin
console.log(child1.age); // 18
console.log(child1.colors); // ["red", "blue", "green", "black"]

var child2 = new Child('daisy', '20');

console.log(child2.name); // daisy
console.log(child2.age); // 20
console.log(child2.colors); // ["red", "blue", "green"]
```

**缺点**：组合继承最大的缺点是会调用两次父构造函数。

一次是设置子类型实例的原型的时候：

```js
Child.prototype = new Parent();
```

一次在创建子类型实例的时候：

```js
var child1 = new Child('kevin', '18');
```

回想下 new 的模拟实现，其实在这句中，我们会执行：

```js
Parent.call(this, name);
```

在这里，我们又会调用了一次 Parent 构造函数。
       所以，在这个例子中，如果我们打印 child1 对象，我们会发现 Child.prototype 和 child1 都有一个属性为colors，属性值为[‘red’, ‘blue’, ‘green’]。

## 原型式继承

本质上，object（）是对传入的对象执行了一次浅复制。将传入的对象作为创建的对象的原型。非常适合不需要单独创建构造函数，但仍然需要在对象间共享信息的场合。

```javascript
function createObj(o) {   //也可以是function object(o){ };
    function F(){}
    F.prototype = o;
    return new F();
}
```

缺点：包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样。

```javascript
var person = {
    name: 'kevin',
    friends: ['daisy', 'kelly']
}

var person1 = createObj(person);
var person2 = createObj(person);

person1.name = 'person1';
console.log(person2.name); // kevin

person1.firends.push('taylor');
console.log(person2.friends); // ["daisy", "kelly", "taylor"]
```

**注意**：修改`person1.name`的值，`person2.name`的值并未发生改变，并不是因为`person1`和`person2`有独立的 name 值，而是因为`person1.name = 'person1'`，给`person1`添加了 name 值，并非修改了原型上的 name 值。

## 寄生式继承

创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。

```javascript
function createObj (o) {
    var clone = Object.create(o);    //通过调用函数创建一个新对象
    clone.sayName = function () {    //以某种方式增强这个对象
        console.log('hi');
    }
    return clone;
}
```

**缺点**：跟借用构造函数模式一样，每次创建对象都会创建一遍方法。

## 寄生组合式继承

组合继承最大的缺点是会调用两次父构造函数。

而寄生组合式继承只调用一次父构造函数。基本思路是不通过调用父类构造函数给子类原型赋值，而是取得父类原型的一个副本。（使用寄生式继承来继承父类原型，然后将返回的新对象赋值给子类原型）

```javascript
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

function prototype(child, parent) {
    var prototype = object(parent.prototype);
    prototype.constructor = child;
    child.prototype = prototype;
}

// 当我们使用的时候：
prototype(Child, Parent);

```

```js
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}

function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
    // 通过构造一个介于 Parent 与 Child 之间的对象，并使该对象的 prototype 属性指向 Parent 的 prototype对象，
    // 来避开通过调用 Parent 构造函数的方式来产生一个 prototype 指向Parent prototype对象的对象。
}

function prototype(child, parent) {
// 不直接child.prototype=parent.prototype呢？
// 原因 : 当我们想给 Child 的prototype里面添加共享属性或者方法时，如果其 prototype 指向的是 Parent 的 prototype，那么在 Child 的 prototype 里添加的属性和方法也会反映在 Parent 的 prototype 里面，
// 这明显是不合理的，这样做的后果是当我们只想使用 Parent 时，也能看见 Child 往里面扔的方法和属性。
// 所以需要每个构造函数都需要持有自己专用的prototype对象
    var prototype = object(parent.prototype);
    prototype.constructor = child;
    child.prototype = prototype;
}

prototype(Child, Parent);

var child1 = new Child('kevin', '18');

console.log(child1);
-----------------------------------------------------------------------------------------
//简便版

function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}
Child.prototype = Object.create(Parent.prototype)     //可以直接这么用，就不用写上面那一堆了
Child.prototype.nn = "sss"

var child1 = new Child('kevin', '18');

console.log(child1);
console.log(child1.__proto__);
```

这种方式的高效率体现它只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf。

寄生式组合继承可以算是**引用类型**继承的最佳模式。

## 类继承

```js
//基于class实现继承  核心：无需额外对子类的原型对象进行修改，在子类声明时已经完成继承（子类的原型对象以父类的原型对象为原型）

class Animal{
  constructor(name){
    this.name = name
    this.colors = ['black','white']
  }
  
  getName(){
    return this.name
  }
}

class Dog extends Animal{
  constructor(name, age){
    super(name)
    this.age = age
  }
}
```

# Set、Map、WeakSet、WeakMap

**Set**对象可以存储**任何类型**的数据。 值是唯一的，**没有重复**的值。

**Map**对象保存**键值对**，任意值都可以成为它的键或值。

**WeakSet** 结构与 **Set** 类似，也是不重复的值的集合 . **WeakMap** 对象是一组**键值对**的集合

不同： `WeakSet` 的成员只能是**对象**，而不能是其他类型的值。 **WeakSet 不可遍历**。

`WeakMap`只接受**对象作为键名**（`null`除外），不接受其他类型的值作为键名。

`WeakMap`的键名所指向的对象，**不计入**垃圾回收机制。

# new一个对象的过程

因此new操作符创建对象可以分为以下四个步骤：

- 在内存中创建一个空对象
- 将所创建对象的__proto__属性值设为构造函数的prototype的属性值
- 构造函数内部的this指向该对象
- 执行构造函数内部的代码（给新对象添加属性）
- 返回对象

# JSON

Json语法支持表示**3**种类型的值。

- 简单值: 字符串、数值、布尔值和null
- 对象：Json中的对象必须使用双引号把属性名包围起来。（没有变量声明，最后没有分号）
- 数组：（没有变量，没有分号）

JSON对象有两个方法：**stringify()**和**parse()**。分别为：将Javascript序列化为JSON字符串，以及将JSON解析为原生JavaScript值。

JSON.stringify()方法除了第一个参数之外（需要序列化的对象），还可以额外接收两个参数，第二个参数可以是一个数组（起过滤作用），也可以是一个函数（函数内接收key和value两个参数，主要也是起过滤作用）；第三个参数控制缩进和空格，在这个参数是数值时，表示每一级缩进的空格数。

JSON.parse()方法也可以接受一个额外的参数。（一个函数包含key和value两个参数）

# Ajax原理

Ajax在用户和服务器之间加了一个**中间层**（AJAX引擎），通过XMLHttpRequest对象来向服务器发**异步请求**，从服务器获取数据，然后用javascript来操作DOM而更新页面。Ajax的过程只涉及Javascript、XMLHttpRequest和DOM。**XMLHttpRequest是Ajax的核心机制**。

发送Ajax请求的五个步骤（XMLHttpRequest的工作原理）：

1. 创建XMLHttpRequest对象
2. 使用open方法设置请求的参数 ——open（method,url,是否异步）
3. 发送请求
4. 注册事件——注册onreadystatechange事件，状态改变时就会调用
5. 获取返回的数据，更新UI

```javascript
let xhr = new XMLHttpRequest();
xhr.onreadystatechange = function(){
    if(xhr.readyState === 4){   // readyState有0,1,2,3,4五种状态
        if((xhr.status>=200 && xhr.status<300)||xhr.status === 304){
            alert(xhr.responseText);
        }else{
            alert("加载失败"+xhr.status)
        }
    }
}
xhr.open("get","url",true/false);//是否异步 
xhr.send(null);

```

注意：这里为了保证跨浏览器兼容。onreadystatechange事件处理程序应该在调用open()之前赋值。如上代码所示。

发送额外的请求**头部**，可以使用XMLHttpRequest对象的**setRequestHeader()**方法（方法接收两个参数：头部字段的名称和值），必须在open()之后，send()之前调用。

可以使用XMLHttpRequest对象的getResponseHeader()方法从XMLHttpRequest对象获取响应头部（只要传入想要获取头部的名称即可）。若想取得所有响应头部，可以使用getAllRequestHeaders()方法。

## 发送get请求时，URL后面添加查询字符串参数的方法

可以使用下面的函数将查询字符串参数添加到现有的URL末尾(查询字符串中的每个名和值都必须使用endodeURIComponent()编码)：

```javascript
function addURLParam(url,name,value){
    url += (url.indexOf("?") === -1 ? "?":"&");
    url += encodeURIComponent(name)+"="+encodeURIComponent(value);
    return url;
}
```

# == 和 ===的区别

===要求数据类型相同， ==会进行隐式类型转换

# JavaScript的内置对象

| 内置对象  | 对象说明       |
| --------- | -------------- |
| Arguments | 函数参数集合   |
| Array     | 数组           |
| Boolean   | 布尔对象       |
| Math      | 数学对象       |
| Date      | 日期时间       |
| Error     | 异常对象       |
| Function  | 函数构造器     |
| Number    | 数值对象       |
| Object    | 基础对象       |
| RegExp    | 正则表达式对象 |
| String    | 字符串对象     |

# 接口调用的方式

1. 原生Ajax，基于Query的Ajax
2. Promise
3. Fetch
4. axios

# 实现异步编程的方式？

- callback 异步解决方案（回调函数（事件监听、发布订阅等都属于回调函数））
- Promise 异步解决方案（解决回调地狱）
- async await 语法糖
- Generator 异步解决方案（遍历对象生成器）

[一文吃透 Js 异步编程](https://juejin.cn/post/6986673391029714958)

# Promise

Promise是一种异步解决方案，Promise对象接受**一个回调函数**作为参数, 该回调函数**接受两个参数**，分别是成功时的回调**resolve**和失败时的回调**reject**；另外resolve的参数除了正常值以外， 还可能是一个Promise对象的实例；reject的参数通常是一个Error对象的实例。

1. 优点就是更好的异步解决方案
2. 缺点就是无法取消Promise，一旦新建它就会立即执行，无法中途取消

# promise怎么实现链式调用跟返回不同的状态

实现链式调用：使用`.then()`或者`.catch()`方法之后会返回一个`promise`对象，可以继续用`.then()`方法调用，再次调用所获取的参数是上个`then`方法`return`的内容

1. promise的三种状态是 `fulfilled`(已成功)/`pengding`(进行中)/`rejected`(已拒绝)
2. 状态只能由 Pending --> Fulfilled 或者 Pending --> Rejected，且一但发生改变便不可二次修改；
3. Promise 中使用 `resolve` 和 `reject` 两个函数来更改状态；
4. then 方法内部做的事情就是状态判断:

- 如果状态是成功，调用成功回调函数
- 如果状态是失败，调用失败回调函数

# 函数柯里化

`柯里化(Currying)` 是把接收多个参数的原函数变换成接受一个单一参数（原来函数的第一个参数的函数)并返回一个新的函数，新的函数能够接受余下的参数，并返回和原函数相同的结果。

柯里化的函数可以延迟接收参数，就是比如一个函数需要接收的参数是两个，执行的时候必须接收两个参数，否则没法执行。但是柯里化后的函数，可以先接收一个参数。

```js
// 普通的add函数
function add(x, y) {
    return x + y
}
// Currying后
function curryingAdd(x) {
    return function (y) {
        return x + y
    }
}
add(1, 2)           // 3
curryingAdd(1)(2)   // 3
```

# 什么时候不能用箭头函数

## 1. 定义对象方法

 JS 中对象方法的定义方式是在对象上定义一个指向函数的属性，当方法被调用的时候，方法内的 this 就会指向方法所属的对象。

```js
let obj = {
    array: [1, 2, 3],
    sum: () => {
        console.log(this === window); // true
        return this.array.reduce((result, item) => result + item);
    }
};
console.log(this === window); //true
obj.sum();//报错：Uncaught TypeError: Cannot read property 'reduce' of undefined at Object.sum
```

运行时 this.array 是未定义的，调用 obj.sum 的时候，执行上下文里面的 this 仍然指向的是 window，原因是箭头函数把函数上下文绑定到了 window 上，this.array 等价于 window.array，显然后者是未定义的。

修改方式：使用函数表达式或者方法简写（ES6 中已经支持）来定义方法，这样能确保 this 是在运行时是由包含它的上下文决定的。代码如下：

```js
let obj = {
    array: [1, 2, 3],
    sum() {
        console.log(this === window); // false
        return this.array.reduce((result, item) => result + item);
    }
};
console.log(this === window); //true
console.log(obj.sum());//6
```

## 2.定义原型方法

同样的规则适用于原型方法（prototype method）的定义，使用箭头函数会导致运行时的执行上下文错误。比如下面代码：

```js
function Cat(name) {
    this.name = name;
}

Cat.prototype.sayCatName = () => {
    console.log(this === window); // => true
    return this.name;
};

const cat = new Cat('Tom');
console.log(cat.sayCatName()); // undefined
```

使用传统的函数表达式就能解决问题，代码如下所示：

```js
function Cat(name) {
    this.name = name;
}

Cat.prototype.sayCatName = function () {
    console.log(this === window); // => false
    return this.name;
}

const cat = new Cat('Tom');
console.log(cat.sayCatName()); // Tom
```

sayCatName 变成普通函数之后，被调用时的执行上下文就会指向新创建的 cat 实例。

## 3. 定义事件回调函数

箭头函数在声明的时候就绑定了执行上下文，要动态改变上下文是不可能的，在需要动态上下文的时候它的弊端就凸显出来。

比如在客户端编程中常见的 DOM 事件回调函数（event listenner）绑定，触发回调函数时 this 指向当前发生事件的 DOM 节点，而动态上下文这个时候就非常有用，比如下面这段代码试图使用箭头函数来作事件回调函数。

```js
const button = document.getElementById('myButton');
button.addEventListener('click', () => {
    console.log(this === window); // true
    this.innerHTML = 'Clicked button';
});
```

在全局上下文下定义的箭头函数执行时 this 会指向 window，当单击事件发生时，this.innerHTML 就等价于 window.innerHTML，而后者是没有任何意义的。

使用函数表达式就可以在运行时动态的改变 this，修正后的代码：

```js
const button = document.getElementById('myButton');
button.addEventListener('click', function () {
    console.log(this === button); // true
    this.innerHTML = 'Clicked button';
});
```

## 4. 定义构造函数

构造函数中的 this 指向新创建的对象，当执行 new Car()  的时候，构造函数 Car 的上下文就是新创建的对象，也就是说 this instanceof Car ===  true。显然，箭头函数是不能用来做构造函数， 实际上 JS 会禁止你这么做，如果你这么做了，它就会抛出异常。

比如下面的代码就会报错：

```js
const Message = (text) => {
    this.text = text;
};
const helloMessage = new Message('Hello World!');//报错： Throws "TypeError: Message is not a constructor"
```

构造新的 Message 实例时，JS 引擎抛了错误，因为 Message 不是构造函数。可以通过使用函数表达式或者函数声明来声明构造函数修复上面的例子。

```js
const Message = function(text) {
    this.text = text;
};
const helloMessage = new Message('Hello World!');
console.log(helloMessage.text); // 'Hello World!'
```













# DOM事件的级别

DOM事件的级别，准确来说，是**DOM标准**定义的级别。包括：

## DOM0

```js
element.onclick = function () { 
  // TODO  
}
```

## DOM2

##### addEventListener（高版本浏览器）

```js
element.addEventListener('click', function () { 
    // TODO
}, false);
```

参数解释：

- 参数1：事件名的字符串(注意，没有on)
- 参数2：回调函数：当事件触发时，该函数会被执行
- 参数3：**true表示捕获阶段触发，false表示冒泡阶段触发（默认）**。如果不写，则默认为false。

###### 示例：

```html
<body>
<button>按钮</button>
<script>
    var btn = document.getElementsByTagName("button")[0];
    btn.addEventListener("click", fn1);
    btn.addEventListener("click", fn2);
    function fn1() {
        console.log("事件1");
    }
    function fn2() {
        console.log("事件2");
    }
</script>
</body>
// 输出： 事件1
//       事件2
```

`addEventListener()`这种绑定事件的方式：

- 一个元素的一个事件，可以绑定多个响应函数。**不存在响应函数被覆盖的情况**。执行顺序是：**事件被触发时，响应函数会按照函数的绑定顺序执行。**
- **addEventListener()中的this，是绑定事件的对象**。
- `addEventListener()`不支持 IE8 及以下的浏览器。在IE8中可以使用`attachEvent`来绑定事件。

##### attachEvent（IE8及以下版本浏览器）

```javascript
element.attachEvent('onclick', function () {
    // TODO
});
```

参数解释：

- 参数1：事件名的字符串(注意，有on)
- 参数2：回调函数：当事件触发时，该函数会被执行

###### 示例：

```html
<body>
  <button>按钮</button>
<script>
  var btn = document.getElementsByTagName('button')[0];
  btn.attachEvent('onclick', function() {
    console.log('事件1');
  });

  btn.attachEvent('onclick', function() {
    console.log('事件2');
  });
</script>
</body>
// 输出：事件2
//      事件1
```

`attachEvent()`这种绑定事件的方式：

- 一个元素的一个事件，可以绑定多个响应函数。**不存在响应函数被覆盖的情况**。**注意**：执行顺序是，后绑定的先执行。
- **attachEvent()中的this，是window**。

##### 兼容代码

```html
<body>
  <button>按钮</button>
<script>
  var btn = document.getElementsByTagName('button')[0];
  myBind(btn , "click" , function(){
    alert(this);
  });

  /*
  * 参数：
  *  element 要绑定事件的对象
  *  eventStr 事件的字符串(不要on)
  *  callback 回调函数
  */
  function myBind(element , eventStr , callback){
    if(element.addEventListener){
      //大部分浏览器兼容的方式
      element.addEventListener(eventStr , callback , false);
    }else{
      //IE8及以下
      element.attachEvent("on"+eventStr , function(){
        //在匿名函数 function 中调用回调函数callback
        callback.call(element);
      });
    }
  }
</script>
</body>
```

#### DOM3

```js
element.addEventListener('keyup', function () {
    // TODO
}, false);
```

DOM3中，增加了很多事件类型，比如鼠标事件、键盘事件等。

***PS：*为何事件没有DOM1的写法呢？因为，DOM1标准制定的时候，没有涉及与事件相关的内容。**

# DOM事件模型

DOM事件模型讲的就是**捕获和冒泡**。先捕获，再到目标，再冒泡 。捕获是事件会从最外层开始发生，直到最具体的元素。冒泡是事件会从最内层的元素开始发生，一直向上传播，直到window对象。

捕获阶段：事件依次传递的顺序是从 window -> document -> html -> body -> ...(父元素 -> 子元素)-> 目标元素。

冒泡的流程与捕获相反。

# Event 对象常见应用

用户做的是什么操作（比如，是敲键盘了，还是点击鼠标了），这些事件基本都是通过Event对象拿到的。

## 阻止默认事件

```js
 event.preventDefault();
```

比如，已知`<a>`标签绑定了click事件，此时，如果给`<a>`设置了这个方法，就阻止了链接的默认跳转。

### 阻止冒泡

w3c的方法：（火狐、谷歌、IE11）

```js
event.stopPropagation();
```

IE10以下则是：

```js
event.cancelBubble = true;
```

兼容代码如下：

```js
// 对box进行了阻止冒泡，事件不会向上冒泡传递到父元素、body、html、document、window
box.onclick = function (event) {
  alert("child");
  //阻止冒泡
  event = event || window.event;
  if (event && event.stopPropagation) {
      event.stopPropagation();
  } else {
    event.cancelBubble = true;
    }
}
```

经常在业务场景中遇到。

### 设置事件优先级

```js
event.stopImmediatePropagation();
```

场景：我用addEventListener给某按钮同时注册了事件A、事件B。此时，如果我单击按钮，就会依次执行事件A和事件B。现在要求：单击按钮时，只执行事件A，不执行事件B。该怎么做呢？这是时候，就可以用到`stopImmediatePropagation`方法了。做法是：在事件A的响应函数中加入该语句。

### event 属性

event 有很多属性，常见属性有如下：

```js
 event.currentTarget   //当前所绑定的事件对象。在事件委托中，指的是【父元素】。
event.target  //当前被点击的元素。在事件委托中，指的是【子元素】。
```

![img](../source/images/js%E5%9F%BA%E7%A1%80/20180203_1739.png)

由于pageX 和 pageY的兼容性不好，我们可以这样做：

- 鼠标在页面的位置 = 滚动条滚动的距离 + 可视区域的坐标。

# 事件委托

通俗地来讲，就是把一个元素响应事件（click、keydown......）的函数委托到另一个元素。

事件委托是利用了**冒泡机制**，减少了事件绑定的次数，减少内存消耗，提高性能。

# 事件冒泡、捕获(委托)

- **事件冒泡**指在在一个对象上触发某类事件，如果此对象绑定了事件，就会触发事件，如果没有，就会向这个对象的父级对象传播，最终父级对象触发了事件。
- **事件委托**本质上是利用了浏览器事件冒泡的机制。因为事件在冒泡过程中会上传到父节点，并且父节点可以通过事件对象获取到目标节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件，这种方式称为**事件代理**。

`event.stopPropagation()` 或者 ie下的方法 `event.cancelBubble = true;` //阻止事件冒泡

# offset、client与scroll的区别

- offsetWidth/offsetHeight 返回值包含 content + padding + border，效果与 e.getBoundingClientRect()相同
- clientWidth/clientHeight 返回值只包含 content + padding，如果有滚动条，也不包含滚动条
- scrollWidth/scrollHeight 返回值包含 content + padding + 溢出内容的尺寸

# 前端模块化

目前流行的js模块化规范有**CommonJS、AMD、CMD以及ES6的模块系统**。

`nodeJS`里面的模块是基于`commonJS`规范实现的，原理是文件的读写，导出文件要使用`exports`、`module.exports`，引入文件用`require`。 每个文件就是一个模块；每个文件里面的代码会用默认写在一个闭包函数里面 。

`AMD`规范则是非同步加载模块，允许指定回调函数，`AMD` 是 `RequireJS` 在推广过程中对模块定义的规范化产出。

`AMD`推崇**依赖前置**, `CMD`推崇**依赖就近**。对于依赖的模块AMD是提前执行，CMD是延迟执行。

在`ES6`中，我们可以使用 `import` 关键字引入模块，通过 `exprot` 关键字导出模块，但是由于ES6目前无法在浏览器中执行，所以，我们只能通过`babel`将不被支持的`import`编译为当前受到广泛支持的 `require`。

CommonJs 和 ES6 模块化的区别：

1. CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
2. CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

[参考](https://juejin.cn/post/6991724298197008421#heading-6)

[前端模块化：CommonJS,AMD,CMD,ES6](https://juejin.cn/post/6844903576309858318)

# import 和 require 导入的区别

import 的ES6 标准模块； require 是 AMD规范引入方式；

import是编译时调用，所以必须放在文件开头;是解构过程 require是运行时调用，所以require理论上可以运用在代码的任何地方;是赋值过程。其实require的结果就是对象、数字、字符串、函数等，再把require的结果赋值给某个变量



# ES6

新增symbol类型 表示独一无二的值，用来定义独一无二的对象属性名;

const/let  都是用来声明变量,不可重复声明，具有块级作用域。存在暂时性死区，也就是不存在变量提升。(const一般用于声明常量);

变量的解构赋值(包含数组、对象、字符串、数字及布尔值,函数参数),剩余运算符(...rest);

模板字符串(`${data}`);

扩展运算符(数组、对象);;

箭头函数;

Set和Map数据结构;

Proxy/Reflect;

Promise;

async函数;

Class;

Module语法(import/export)。