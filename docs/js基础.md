## this的指向问题

![image-20210718220900834](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210718220900834.png)

![image-20210718221929275](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210718221929275.png)

**箭头函数**中本身没有this，导致它会从上一级代码块中寻找this，继承自父执行上下文中的this。

![image-20210718224348911](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210718224348911.png)

## 深拷贝浅拷贝

**浅拷贝**是创建一个新对象，这个对象有着原始对象属性的一份精确拷贝。**如果属性是基本类型，拷贝的就是基本类型的值**，如果属性是引用类型，拷贝的就是内存地址，所以其中一个对象改变了这个地址，就会影响到另一个对象。（浅拷贝，旧对象的属性都拷给新对象了，当属性值是基本类型数据时，如果新对象改变此属性值，则不影响旧对象，当属性值时引用类型数据时，由于拷贝的时内存地址，所以两个对象指向相同的地址，会互相影响）

**深拷贝**是将一个对象从内存中完整地拷贝一份出来，从堆内存中开辟一个新的区域存放新对象，且修改新对象不影响原对象。

### 针对引用类型来说，赋值、深拷贝、浅拷贝的区别

1.浅拷贝、赋值的区别

**赋值：**当我们把一个对象**赋值**给一个新的变量时，赋得其实是该对象在栈中的地址，而不是堆中的数据，也就是两个对象指向的是同一个存储空间，无论哪个对象发生改变，其实都是改变的存储空间的内容，因此两个对象是联动的。

**浅拷贝：**重新在堆中创建内存，拷贝前后对象的基本类型互不影响，但拷贝前后对象的引用类型因共享一块内存，会相互影响。

**深拷贝：**从堆内存中开辟一个新的区域存放对象，对对象中的子对象进行递归拷贝，拷贝前后的两个对象互不影响。

![image-20210719152003990](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210719152003990.png)

深、浅拷贝实现方式：

  ![image-20210719152921774](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210719152921774.png)

**注意：**函数、日期、正则格式的数据用JSON.parse(JSON.stringify())方式进行深拷贝，会变成空对象，会把undefined去掉。（用此方法进行深拷贝的弊端）



## js的作用域

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

### **作用域的深层次理解**

**执行期的上下文**
	    当函数代码执行的前期会创建一个执行期上下文的内部对象A0(作用域)

这个内部的对象是预编译的时候创建出来的因为当函数被调用的时候会先进行预编译

在全局代码执行的前期会创建一个执行期的上下文的对象GO

### 函数作用域预编译

1. 创建ao对象 A0{}
2. 找形参和变量声明  将变量和形参名当做AO对象的属性名    值为undefined
3. 实参形参相统一
4. 在函数体里面找函数声明   值赋予函数体

### 全局作用域的预编译

1. 创建GO对象
2. 找变量声明  将变量名作为GO对象的属性名    值是undefined
3. 找函数声明  值赋予函改体

![image-20210718214140121](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210718214140121.png)

预编译完进行解释执行操作。

## 闭包

简单理解闭包就是函数里面return函数。





## 防抖节流(待补充)

**防抖：**当持续触发事件（比如键盘一直输入内容），一定时间内没有再触发事件（键盘抬起了并且满足在一定时间内没有再按下去），事件处理函数才会执行一次。

肯定是定时器。在一段时间内，如果又触发了事件，重新开始延时，代表的就是重新开始定时器。

**防抖实际应用**如下：

![image-20210719202645876](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210719202645876.png)

**节流：**当持续触发事件的时候，保证一定时间内，只调用一次事件处理函数，一段时间内只做一件事情（每隔一段时间执行一次）。

节流实际应用：鼠标滑动、拖拽

**防抖**和**节流**本质是不一样的，防抖动是将多次执行变为最后一次执行，节流是将多次执行变成每隔一段时间执行。

防抖代码：（输入框一秒内不管输入多少数据，都是一秒后才打印出来）

![image-20210721164939054](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210721164939054.png)



![image-20210721164749030](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210721164749030.png)



## apply、call、bind的用法和区别

call、apply、bind都是改变this指向的方法。（call和apply非常相似，只是传参方式不同）

- apply 、 call 、bind 三者都是用来改变函数的this对象的指向的；

- apply 、 call 、bind 三者第一个参数都是this要指向的对象，也就是想指定的上下文；

- apply 、 call 两者都可以利用后续参数传参； 但是传参的方式不一样，apply是数组，call是正常传参形式

- bind 返回this指向已经变化的函数，需要手动调用；apply 、call 则是在指向变化后立即调用 。

  比如下面的这个例子。

  一个叫喵喵的猫喜欢吃鱼，一个叫汪汪的小狗喜欢啃骨头，有一天，小狗汪汪和喵喵共进午餐的时候，汪汪说自己想尝尝小鱼干的味道，但是因为有刺，喵喵就想了个办法，说自己先吃，完了喂给汪汪。

![img](../../source/images/js%E5%9F%BA%E7%A1%80/20180517112432197)

这样，汪汪就吃到了美味的鱼干。可是汪汪每吨都想来点小鱼干，喵喵还要工作去捉老鼠，所以它们又想了一个办法，喵喵把吃鱼的方法教给汪汪。这样，每次汪汪就可以自己吃小鱼干了。

![https://img-blog.csdn.net/20180517112744838?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTAxNzYwOTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70](../../source/images/js%E5%9F%BA%E7%A1%80/20180517112744838)

bind()方法注意：bind的时候传的参数会预先传给返回的方法，调用方法时就不用再传参数了。

如果call()和apply()的第一个参数是null或者undefined，那么this的指向就是全局变量，在浏览器里就是window对象。

![https://img-blog.csdn.net/2018051715053339?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTAxNzYwOTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70](../../source/images/js%E5%9F%BA%E7%A1%80/2018051715053339)

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

## js 的事件循环机制

## 设计模式

**1.单例模式的理解**

定义：1.只有一个实例  2.可以全局访问

主要解决：一个全局使用的类，频繁地创建和销毁

何时使用：当你想控制实例的数目，节省系统化资源的时候

如何实现：判断系统是否已经有这个单例 如果有则返回 没有则创建

单例模式优点：内存中只有一个实例 减少了内存的开销 尤其是频繁地创建和销毁实例 （比如说是首页页面的缓存）

使用场景： 1.全局的缓存 2.弹窗

单例模式在创建弹框的应用（es5实现）：![image-20210723160348796](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210723160348796.png)

(es6实现)

![image-20210723163810624](../../source/images/js%E5%9F%BA%E7%A1%80/image-20210723163810624.png)

**2.策略模式**

**3.发布订阅模式**



## 立即执行函数

所谓立即执行函数即函数定义完后立即执行。

```javascript
function fn(){}()  //错误的，解析器会理解成一个函数定义，后面不应该有括号，所以就是错的。应该用（）包起来

(function(){}()) //正确！

(function(){})() //正确！
```

立即执行函数会形成一个单独的作用域，我们可以封装一些临时变量或者局部变量，避免污染全局变量。

## 遍历数组的方式



## 数组扁平化处理

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

```

