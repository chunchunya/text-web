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