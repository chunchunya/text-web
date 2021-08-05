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

3、块级作用域

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

# 闭包

简单理解闭包就是函数里面return函数。





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



# js 的事件循环机制

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

## ForEach()方法





# 数组的一些方法

## reduce方法

```javascript
(1)reduce(function(prev,cur,index,arr){...},init) //有初始值   即当有初始值的时候，prev代表的是init，cur代表的是数组的第一项

(2)reduce(function(prev,cyr,index,arr){...})  //无初始值  即当没有初始值的时候，prev代表的是数组的第一项，cur代表的是数组的第二项
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
        if(Array.isArray(arr[i])){
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

# 原型链

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

# 继承

## 原型链继承



## 构造函数继承



## 组合继承



## 原型式继承



## 寄生式继承



## 寄生组合式继承

# new一个对象的过程

因此new操作符创建对象可以分为以下四个步骤：

- 在内存中创建一个空对象
- 将所创建对象的__proto__属性值设为构造函数的prototype的属性值
- 构造函数内部的this指向该对象
- 执行构造函数内部的代码（给新对象添加属性）
- 返回对象