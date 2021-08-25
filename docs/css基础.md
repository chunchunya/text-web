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

# box-sizing属性的值：

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

# 如何清除浮动

抄本本上的。

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

## 行内元素

```css
水平居中：text-align: center;

垂直居中：.father{
    		height:20px;
    		line-height:20px;
}
```

## 块级元素

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

