参考文章，[感谢大佬](https://segmentfault.com/a/1190000022021557#item-23)

# 行内元素和块级元素

**块级元素**自成一块，独占一行，默认情况，宽度自动充满父级宽度！

**块级元素**可以设置宽(width)和高(height)属性。值得注意的是块级元素即使设置了宽度，仍然是独占一行。(我是块级元素，我就是这么霸道)

**块级元素**可以设置margin和padding的属性。

**display：block**；与之相对应。

**行内元素**

只要不专门设置(**display: block || display: inline-block**),就永远不会独占一行；而且，从来不会排斥其他的行内元素，只要相邻，就会和他们排在同一行里，只要父级宽度够长，就永远不会换行。

**行内元素**的宽(width)和高(height)属性无效，宽高都是由内容区决定。

display：inline；与之相对应。

行内元素做一个**变形手术**。就会具有块级元素的属性。通过设置**display：block**；就会达到效果。当然,也可以具有块级元素和行内元素共同的特性。通过设置**display：inline-block**；

常见的行内元素：a、b、span、img、input、strong、select、label、em、button

常见的块级元素： div、p、table、td、tr、ul、li、fieldset、legend！

# flex布局的常用属性

1. flex-direction:   row / colum / row-reverse / colum-reverse
2. flex-wrap:  控制子元素溢出时的换行处理
3.  justify-conent:  设置子元素在主轴上的对齐方式
4. align-items:  设置子元素在侧轴上的对齐方式
5. flex:  1/2/3   设置子盒子的权重

flex:  1, 即为flex-grow:  1.常用作自适应布局。

# box-sizing属性的值

- content-box，默认值，遵从标准盒模型；
- border-box，使用IE盒模型；
- inherit，继承父元素的box-sizing属性值；

# JS如何设置、获取盒模型对应的宽和高

**1、通用型的**

```css
   window.getComputedStyle(element).width/height;
```

能兼容火狐、Chrom等浏览器。

**2、通过DOM节点的 style 样式获取**

```css
    element.style.width/height;
```

缺点：通过这种方式，只能获取**行内样式**，不能获取`内嵌`的样式和`外链`的样式。

这种方式有局限性，但应该了解。

**3、IE独有的**

```css
    element.currentStyle.width/height;
```

这种方式只有IE独有。获取到的即时运行完之后的宽高（三种css样式都可以获取）。

**4、**

```css
    element.getBoundingClientRect().width/height;
```

此 api 的作用是：获取一个元素的绝对位置。绝对位置是视窗 viewport 左上角的绝对位置。

此 api 可以拿到四个属性：left、top、width、height。

# 对 BFC 规范（块级格式化上下文：blockformattingcontext）的理解？

```scss
概念：
    BFC指的是块级格式化上下文，一个元素形成了BFC之后，那么它内部元素产生的布局不会影响到外部元素，外部元素的布局也不会影响到BFC中的内部元素。一个BFC就像是一个隔离区域，和其他区域互不影响。
布局规则如下：
    1.内部的盒子会在垂直方向，一个个地放置；
    2.盒子垂直方向的距离由margin决定， 属于同一个BFC的两个相邻Box的上下margin会发生重叠；
    3.每个元素的左外边缘（margin-left），与包含的盒子的左边缘（border-left）相接触，即使存在浮动也是如此；
    4.BFC的区域不会与float的元素区域重叠，而是紧贴浮动边缘；
    5.在计算BFC高度时，自然也会检测浮动或者定位的盒子高度；
    6.计算BFC的高度时，浮动元素也参与计算。 
```

```CSS
创建BFC：
（1）根元素或包含根元素的元素
（2）浮动元素float＝left|right或inherit（≠none）
（3）绝对定位元素position＝absolute或fixed
（4）display＝inline-block|flex|inline-flex|table-cell或table-caption|block|list-item|table
（5）overflow＝hidden|auto或scroll(≠visible)
```

### BFC常见的应用

**1、解决普通文档流块元素的外边距折叠问题**

```html
<style>
  div {
    width: 100px;
    height: 100px;
  }   
  .child1 {
    margin: 10px 0;
    background-color: blueviolet;
  }
  .child2 {
    margin: 20px 0;
    background-color: pink;
  }
</style>
<body>
  <div class="parent">
    <div class="child1"></div>
    <div class="child2"></div>
  </div>
</body>
```

![img](../source/images/css%E5%9F%BA%E7%A1%80/007S8ZIlly1gjdfwuwl15j32800rutdz.jpg)

从图中可以看出，两个块元素外边距为20px，出现边距重叠问题。可以通过将两个块元素置于不同的BFC中进行隔离解决。

```html
<style>
  .parent div {
    width: 100px;
    height: 100px;
  }
  /* BFC */
  .parent {
    overflow: hidden;
  } 
  .child1 {
    margin: 10px 0;
    background-color: blueviolet;
  }
  .child2 {
    margin: 20px 0;
    background-color: pink;
  }
</style>
<body>
  <!-- 使用BFC解决问题 -->
    <div class="parent">
        <div class="child1"></div>
    </div>
    <div class="parent">
        <div class="child2"></div>
    </div>
</body>
```

![image-20201004182953246](../source/images/css%E5%9F%BA%E7%A1%80/007S8ZIlly1gjdhauiw8nj32800riag5.jpg)

**2、BFC清除浮动**

子元素设置浮动，脱离文档流，导致父元素坍塌，没有高度，可以通过设置BFC，在父元素添加`overflow: hidden`清除浮动。

**清除浮动前**

```css
<style>
  .parent {
    border: 1px solid red;
  }
    .child {
      float: left;
    width: 100px;
    height: 100px;
    background-color: pink;
    }
</style>
<body>
  <div class="parent">
    <div class="child"></div>
  </div>
</body>
```

![清除浮动前](../source/images/css%E5%9F%BA%E7%A1%80/007S8ZIlly1gjdhygqcrcj30tq06uq2w.jpg)

**清除浮动后**

```html
<style>
  .parent {
    border: 1px solid red;
    /* 清除浮动 */
    overflow: hidden;
    zoom: 1; /* 兼容低版本 */
  }
    .child {
      float: left;
    width: 100px;
    height: 100px;
    background-color: pink;
    }
</style>
<body>
  <div class="parent">
    <div class="child"></div>
  </div>
</body>
```

![清除浮动后](../source/images/css%E5%9F%BA%E7%A1%80/007S8ZIlly1gjdhwifdnoj30u0082q2x.jpg)

**3、解决普通文档流元素被浮动元素覆盖问题**

在父元素中有两个子元素，其中一个设置浮动，一个没有设置，会导致没有设置浮动的元素被浮动元素覆盖的问题。

```css
<style>
.child1 {
  float: left;
  width: 100px;
  height: 100px;
  background-color: pink;
}

.child2 {
  width: 200px;
  height: 200px;
  background-color: blueviolet;
}
</style>
<body>
<div class="parent">
<div class="child1">左侧浮动元素</div>
<div class="child2">没有设置浮动元素，没有触发BFC</div>
</div>
</body>
```

![image-20201004190527423](../source/images/css%E5%9F%BA%E7%A1%80/007S8ZIlly1gjdibuyrhtj30tg0bwdgh.jpg)

触发BFC解决覆盖问题。

```css
<style>
.child1 {
  float: left;
  width: 100px;
  height: 100px;
  background-color: pink;
}

.child2 {
  width: 200px;
  height: 200px;
  background-color: blueviolet;
  /* 触发BFC */
  overflow: hidden;
}
</style>
<body>
<div class="parent">
<div class="child1">左侧浮动元素</div>
<div class="child2">没有设置浮动元素，没有触发BFC</div>
</div>
</body>
```

![img](../source/images/css%E5%9F%BA%E7%A1%80/007S8ZIlly1gjdiep1u51j30tm0bkgm7.jpg)

# IFC 是什么？

```undefined
IFC指的是行级格式化上下文，它有这样的一些布局规则：
（1）行级上下文内部的盒子会在水平方向，一个接一个地放置。
（2）当一行不够的时候会自动切换到下一行。
（3）行级上下文的高度由内部最高的内联盒子的高度决定。
```

# 如何清除浮动？

1、在父元素内最后一行增加一个元素标签，比如 div 设置其class,增加样式`clear:both`。 

```css
<style>
.child {
  float: left;
}
/* 清除浮动 */
.clearfix {
  clear: both;
}
</style>
<body>
  <div class="parent">
    <div class="child"></div>
        /* 增加一行，设置样式清除浮动 */
        <div class="clearfix"></div>
  </div>
</body>
```

> **缺点：产生大量的空元素，浪费资源。**

2、BFC清除浮动，在父元素class里面添加样式overflow:hidden;zoom:1;(兼容低版本)。 

```html
<style>
  .parent {
    overflow: hidden;
    zoom: 1; // 兼容低版本
  }
    .child {
      float: left;
    }
</style>
<body>
  <div class="parent">
    <div class="child"></div>
  </div>
</body>
```

3、在父元素class添加伪元素（最推荐）

```css
<style>
.parent::after{
  content:'';
  display:block;
  clear:both;
}
/* 兼容 IE */
.parent {
  zoom: 1;
}
</style>
<body>
  <div class="parent">
    <div class="child"></div>
  </div>
</body>
```

```coq
注：clear属性只有块级元素才有效的，而::after等伪元素默认都是内联水平，这就是借助伪元素清除浮动影响时需要设置disp
lay属性值的原因。
```

[参考文章](https://www.cnblogs.com/anlianghui/p/13784485.html)

# css选择器的种类

1、标签选择器（如：body,div,p,ul,li）。

2、.类选择器（如：class=“head”,class=“head_logo”） 。

3、ID选择器（如：id=“name”,id=“name_txt”）。

4、全局选择器（如：*号）。

5、.组合选择器（如：.head .head_logo,注意两选择器用空格键分开）。

6、.继承选择器（如：div p,注意两选择器用空格键分开）。

7、伪类选择器（如：就是链接样式,a元素的伪类，4种不同的状态：link、visited、active、hover。）。

8、字符串匹配的属性选择符(^ $ *三种，分别对应开始、结尾、包含) 。

[参考文章](https://blog.csdn.net/sc13647346287/article/details/83055890)

# 两栏布局

```text
两栏布局一般指的是页面中一共两栏，左边固定，右边自适应的布局，一共有四种实现的方式。

以左边宽度固定为 200px 为例

-（1）利用浮动，将左边元素宽度设置为 200px，并且设置向左浮动。将右边元素的 margin-left 设置为 200px，宽度设置为 auto（默认为 auto，撑满整个父元素）。

-（2）第二种是利用 flex 布局，将左边元素的放大和缩小比例设置为 0，基础大小设置为 200px。将右边的元素的放大比例设置为 1，缩小比例设置为 1，基础大小设置为 auto。

-（3）第三种是利用绝对定位布局的方式，将父级元素设置相对定位。左边元素设置为 absolute 定位，并且宽度设置为 200px。将右边元素的 margin-left 的值设置为 200px。

-（4）第四种还是利用绝对定位的方式，将父级元素设置为相对定位。左边元素宽度设置为 200px，右边元素设置为绝对定位，左边定位为 200px，其余方向定位为 0。
```

# 三栏布局

```text
三栏布局一般指的是页面中一共有三栏，左右两栏宽度固定，中间自适应的布局，一共有五种实现方式。

这里以左边宽度固定为100px，右边宽度固定为200px为例。

（1）利用绝对定位的方式，左右两栏设置为绝对定位，中间设置对应方向大小的margin的值。

（2）利用flex布局的方式，左右两栏的放大和缩小比例都设置为0，基础大小设置为固定的大小，中间一栏设置为auto。

（3）利用浮动的方式，左右两栏设置固定大小，并设置对应方向的浮动。中间一栏设置左右两个方向的margin值，注意这种方式，中间一栏必须放到最后。

（4）圣杯布局，利用浮动和负边距来实现。父级元素设置左右的padding，三列均设置向左浮动，中间一列放在最前面，宽度设置为父级元素的宽度，因此后面两列都被挤到了下一行，通过设置margin负值将其移动到上一行，再利用相对定位，定位到两边。双飞翼布局中间列的宽度不能小于两边任意列的宽度，而双飞翼布局则不存在这个问题。

（5）双飞翼布局，双飞翼布局相对于圣杯布局来说，左右位置的保留是通过中间列的margin值来实现的，而不是通过父元素的padding来实现的。本质上来说，也是通过浮动和外边距负值来实现的。
```

# 水平垂直居中

### 行内元素

```css
水平居中：text-align: center;

垂直居中：.father{
    		height:20px;
    		line-height:20px;
}
```

### 块级元素

```css
对于宽高固定的元素

（1）我们可以利用margin:0 auto;/margin:auto;来实现元素的水平居中。

（2）利用绝对定位(父元素设置相对定位)，设置四个方向的值都为0（left:0;right:0--水平居中/top:0;bottom:0--垂直居中），并将margin设置为auto，由于宽高固定，因此对应方向实现平分，可以实现水平和垂直方向上的居中。

（3）利用绝对定位(父元素设置相对定位)，先将元素的左上角通过top:50%和left:50%定位到页面的中心，然后再通过margin负值来调整元素的中心点到页面的中心。
                            .father{
                                position:relative;
                            }
                            .son{
                                height:100px;
                                width:200px;
                                position:absolute;
                                top:50%;
                                left:50%;
                                margin-left:-100px;
                                margin-top:-50px;
                            }
对于宽高不定的元素

（4）利用绝对定位(父元素设置相对定位)，先将元素的左上角通过top:50%和left:50%定位到页面的中心，然后再通过translate来调整元素的中心点到页面的中心。
                            .father{
                                position:relative;
                            }
                            .son{
                                height:100px;
                                width:200px;
                                position:absolute;
                                top:50%;
                                left:50%;
                                transform: translate(-50%, -50%);
                            }

（5）父元素使用flex布局，通过align-items:center和justify-content:center设置容器的垂直和水平方向上为居中对齐，然后它的子元素也可以实现垂直和水平的居中。

（6）父元素使用flex布局，子元素设置margin:auto来进行水平垂直居中（margin: 0 auto为水平居中）。
```

# 伪类和伪元素的区别

**伪类**用于当已有的元素处于某个状态时，为其添加对应的样式，这个状态是根据用户行为而动态变化的。比如说，当用户悬停在指定的元素时，我们可以通过:hover来描述这个元素的状态。

**伪元素**用于创建一些不在文档树中的元素，并为其添加样式。它们允许我们为元素的某些部分设置样式。比如说，我们可以通过::before来在一个元素前增加一些文本，并为这些文本添加样式。虽然用户可以看到这些文本，但是这些文本实际上不在文档树中。

**区别总结起来就是**伪类的操作对象是文档树中已有的元素，而伪元素则创建了一个文档数外的元素。因此，伪类与伪元素的区别在于：有没有创建一个文档树之外的元素。

[总结伪类伪元素](http://www.alloyteam.com/2016/05/summary-of-pseudo-classes-and-pseudo-elements/)

# display:none/visibility:hidden/opacity:0之间的区别

![image-20210828154316525](../source/images/css%E5%9F%BA%E7%A1%80/image-20210828154316525.png)

# CSS3新增伪类

![image-20210831201926379](../source/images/css%E5%9F%BA%E7%A1%80/image-20210831201926379.png)

# CSS3有哪些新特性

```text
新增各种CSS选择器    （:not(.input)：所有class不是“input”的节点）
圆角        （border-radius:8px）
多列布局    （multi-columnlayout）
阴影和反射    （Shadow\Reflect）
文字特效        （text-shadow）
文字渲染        （Text-decoration）
线性渐变        （gradient）
旋转            （transform）
缩放，定位，倾斜，动画，多背景
例如：transform:\scale(0.85,0.90)\translate(0px,-30px)\skew(-9deg,0deg)\Animation:
```

[参考文章](https://juejin.cn/post/6844903518520901639#heading-45)

# flex布局

flex布局是**CSS3新增的一种布局方式**，我们可以通过将一个元素的display属性值设置为flex从而使它成为一个flex
容器，它的所有子元素都会成为它的项目。

## 设置在容器上的（6个）

```CSS
flex-direction: row(默认) | row-reverse | column | column-reverse;

flex-wrap: nowrap(默认) | wrap | wrap-reverse;

flex-flow: row nowrap(默认)    //flex-direction属性和flex-wrap属性的简写形式

justify-content: flex-start（默认） | flex-end | center | space-around | space-
between

align-items: flex-start | flex-end | center | baseline | stretch(默认)

align-content: flex-start | flex-end | center | space-between | space-around | stretch（默认）;
```

## 设置在元素上的（6个）

```css
order:  排列顺序
flex-grow:  空间剩余时的放大比例
flex-shrink:  空间不足时的缩小比例
flex-basis:  缩放前的原始尺寸
flex:  上面三者的集合
align-self:  单独为某元素设置对齐方式
```

[参考文章](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)

# 如果需要手动写动画，你认为最小时间间隔是多久，为什么？（阿里）

```text
多数显示器默认频率是60Hz，即1秒刷新60次，所以理论上最小间隔为1/60*1000ms＝16.7ms
```

# display以及相关的属性

```css
block    块类型。默认宽度为父元素宽度，可设置宽高，换行显示。
none    元素不显示，并从文档流中移除。
inline    行内元素类型。默认宽度为内容宽度，不可设置宽高，同行显示。
inline-block默认宽度为内容宽度，可以设置宽高，同行显示。
list-item    像块类型元素一样显示，并添加样式列表标记。
table    此元素会作为块级表格来显示。
inherit    规定应该从父元素继承display属性的值。
```

# css常用的单位

- `px`：代表像素，是一个相对长度单位，1px占据显示设备上的一个像素点。可以用来指定**字体大小，元素的宽度、高度、边框、内边距，外边距的大小等等**。
  - in - 表示英寸，是一个物理单位，在CSS中被直接映射成为px; 转换的方法是 1in = 96px
  - cm - 表示厘米，在生活中常用的单位，同样被映射为 px; 转换方法为 1cm = 37.8px
  - mm - 表示毫米，与cm类似，转换方法为 1mm = 0.1cm = 3.78px
- `%`： 相对长度单位，相对于父元素的尺寸的取值，实际使用中，如果父元素是一个非稳定的取值，可能会导致父元素被撑开，而实际值取决于其祖先元素中最近的一个拥有稳定取值的元素。整数取值，并不适用于解决自适应问题。
- `em`：是一个相对的长度单位，会根据对象内的font-size大小成倍关系。
  - 优点：能够适应屏幕发生的变化。
  - 缺点：进行设置的时候需要知道父元素的font-size大小。
- `rem`：跟`em`类似，不同之处在于`rem`参考的对象是根元素`html`，因此rem更清晰不易出错。
- `vw`和 `vh`：相对长度单位，`vw`是首屏的宽度，`vh`是首屏的高度。取值范围是[0,100]。适用于依据屏幕宽或高进行的排版，适用于自适应。
- `vmin` 和 `vmax`：相对长度单位，`vmin`是比较首屏的宽高，选取其中最小的作为参考等分100份；`vmax`是比较首屏的宽高，选取其中最大的作为参考等分100份。取值范围是[0,100]。
- `pt` （点）：绝对长度单位，多用于字体尺寸，1px = 0.75pt。

# 常见的元素隐藏方式？

（1）使用 display:none;隐藏元素，渲染树不会包含该渲染对象，因此该元素不会在页面中占据位置，也不会响应绑定的监听事件。

（2）使用 visibility:hidden;隐藏元素。元素在页面中仍占据空间，但是不会响应绑定的监听事件。

（3）使用 opacity:0;将元素的透明度设置为 0，以此来实现元素的隐藏。元素在页面中仍然占据空间，并且能够响应元素绑定的监听事件。

（4）通过使用绝对定位将元素移除可视区域内，以此来实现元素的隐藏。

（5）通过 z-index 负值，来使其他元素遮盖住该元素，以此来实现隐藏。

（6）通过 clip/clip-path 元素裁剪的方法来实现元素的隐藏，这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。

（7）通过 transform:scale(0,0)来将元素缩放为 0，以此来实现元素的隐藏。这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。

（8）overflow:hidden;用来隐藏元素溢出部分，占据空间，无法响应点击事件。

[参考文章](https://juejin.cn/post/6844903456545701901#heading-2)

# 过渡：transition

`transition`的中文含义是**过渡**。过渡是CSS3中具有颠覆性的一个特征，可以实现元素**不同状态间的平滑过渡**（补间动画），经常用来制作动画效果。

- 补间动画：自动完成从起始状态到终止状态的的过渡。不用管中间的状态。
- 帧动画：通过一帧一帧的画面按照固定顺序和速度播放。如电影胶片。

**transition 包括以下属性**：

- `transition-property: all;`  如果希望所有的属性都发生过渡，就使用all。
- `transition-duration: 1s;` 过渡的持续时间。
- `transition-timing-function: linear;`  运动曲线。属性值可以是：
  - `linear` 线性
  - `ease`  减速
  - `ease-in` 加速
  - `ease-out` 减速
  - `ease-in-out`  先加速后减速
- `transition-delay: 1s;` 过渡延迟。多长时间后再执行这个过渡动画。

上面的四个属性也可以写成**综合属性**：

```
	transition: 让哪些属性进行过度 过渡的持续时间 运动曲线 延迟时间;

	transition: all 3s linear 0s;
```

# 转换（transform）

**转换**是 CSS3 中具有颠覆性的一个特征，可以实现元素的**位移、旋转、变形、缩放**，甚至支持矩阵方式。

转换再配合过渡和动画，可以取代大量早期只能靠 Flash 才可以实现的效果。

在 CSS3 当中，通过 `transform` 转换来实现 2D 转换或者 3D 转换。

- 2D转换包括：缩放、移动、旋转。

## 1、缩放：`scale`

格式：

```
	transform: scale(x, y);
	transform: scale(2, 0.5);
```

参数解释： x：表示水平方向的缩放倍数。y：表示垂直方向的缩放倍数。如果只写一个值就是等比例缩放。

取值：大于1表示放大，小于1表示缩小。不能为百分比。

## 2、位移：translate

格式：

```text
	transform: translate(水平位移, 垂直位移);
	transform: translate(-50%, -50%);
```

参数解释：

- 参数为百分比，相对于自身移动。
- 正值：向右和向下。 负值：向左和向上。如果只写一个值，则表示水平移动。

## 3、旋转：rotate

格式：

```text
	transform: rotate(角度);
	transform: rotate(45deg);
```

参数解释：正值 顺时针；负值：逆时针。

## 4 、倾斜 ：skew

使用skew方法实现文字或图像的倾斜处理，在参数中分别指定水平方向上的倾斜角度与垂直方向上的倾斜角度。

    transform：skew（30deg，30deg）；//水平方向上倾斜30度，垂直方向上倾斜30度。

只使用一个参数，省略另一个参数:这种情况下视为只在水平方向上进行倾斜，垂直方向上不倾斜。

    transform：skew（30deg）；
# 动画

```css
    定义动画：
        @keyframes 动画名{
            from{ 初始状态 }
            to{ 结束状态 }
        }
     调用：
      animation: 动画名称 持续时间；
```

其中，animation属性的格式如下：

```css
     animation: 定义的动画名称 持续时间  执行次数  是否反向  运动曲线 延迟执行。(infinite 表示无限次)
     animation: move1 1s  alternate linear 3;
     animation: move2 4s;
```

**举例如下**

```css
 .box {
            width: 100px;
            height: 100px;
            margin: 100px;
            background-color: red;
            /* 调用动画*/
            /* animation: 动画名称 持续时间  执行次数  是否反向  运动曲线 延迟执行。infinite 表示无限次*/
            /*animation: move 1s  alternate linear 3;*/
            animation: move2 4s;
        }

        /* 方式一：定义一组动画*/
        @keyframes move1 {
            from {
                transform: translateX(0px) rotate(0deg);
            }
            to {
                transform: translateX(500px) rotate(555deg);
            }
        }

        /* 方式二：定义多组动画*/
        @keyframes move2 {
            0% {
                transform: translateX(0px) translateY(0px);
                background-color: red;
                border-radius: 0;
            }

            25% {
                transform: translateX(500px) translateY(0px);

            }

            /*动画执行到 50% 的时候，背景色变成绿色，形状变成圆形*/
            50% {
                /* 虽然两个方向都有translate，但其实只是Y轴上移动了200px。
                因为X轴的500px是相对最开始的原点来说的。可以理解成此时的 translateX 是保存了之前的位移 */
                transform: translateX(500px) translateY(200px);
                background-color: green;
                border-radius: 50%;
            }

            75% {
                transform: translateX(0px) translateY(200px);
            }

            /*动画执行到 100% 的时候，背景色还原为红色，形状还原为正方形*/
            100% {
                /*坐标归零，表示回到原点。*/
                transform: translateX(0px) translateY(0px);
                background-color: red;
                border-radius: 0;
            }
        }
```

