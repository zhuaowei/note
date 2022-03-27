# CSS3

## 一、css选择器

### 1.1、常用选择器

#### 1.1.1、元素选择器

选择所有指定元素

```css
a {
    
}
```

#### 1.1.2、id选择器

选择一个元素

```css
#app {
    
}
```

#### 1.1.3、class选择器

选择所有 `class="content"` 的元素

```css
.content {
    
}
```

### 1.2、复合选择器

#### 1.2.1、交集选择器

多个选择器选择的并集。

```css
div.box1 {
    // 选择class为box1的div
}

.a.b.c {
    
}
```

如果多个选择器中有元素选择器，元素选择器要放在前面。

#### 1.2.2、并集选择器

也叫选择器分组，所有被分组的元素都会被选中。

```css
h1, p {
    // 使用 , 分组，选择所有会 h1 元素和 p 元素
}

h1.demo, p.test {
}
```



### 1.3、关系选择器

#### 1.3.1、子元素选择器

选择元素的子元素，只会选择一层。选择直接被div包裹的p元素。

```css
div > p {
    
}
```

#### 1.3.2、后代选择器

选择元素的后代元素。选择所有被div包裹的p元素。

```css
div p {
    
}
```

#### 1.3.3、兄弟选择器

选择有共同父元素的其他元素。

```css
// 只会选择**p标签后面紧挨着的span元素**，如果没有就选不中。
p + span {
    
}

// 选择所有p后面的span元素，有多少选多少，不用挨着
p ~ span {
    
}
```

### 1.4、属性选择器

#### 1.4.1、普通属性选择器

根据属性选择元素

```css
[title] {
    // 选择包含 title 属性的元素
}

p[title] {
    // 选择包含 title 属性的 p 元素
}

p[title=hello] {
    // 选择包含 title 属性，且属性值为 hello 的 p 元素
}
```

#### 1.4.2、正则属性选择器

给属性值匹配正则，满足正则的被选中。

```css
[title ^= a] {
    // 选择属性包含 title，且属性值以 a 开头的元素
}

[title $= b] {
    // 选择属性包含 title，且属性值以 b 结尾的元素
}

[title *= c] {
    // 选择属性包含 title，且属性值包含 c 的元素，不关 c 位置在哪。
}
```

### 1.5、伪类选择器

#### 1.5.1、通用伪类

伪类选择不是真正的类，它是用来描述元素特殊状态的。例如，第一个子元素，鼠标悬停上的，点击的...

```css
ul > li:first-child {
    // 选中 li 的第一个
}

ul > li:last-child {
    // 选中 li 的最后一个
}

ul > li:nth-child(2) {
    // 选中 li 的第二个
    // 如果是 n，则会选中所有 li，n 表示 0 -> 正无穷
    // 2n 选中偶数个，或者写 even；2n + 1 奇数个，或者写 odd
}
```

**这些伪类的序号是与被选中的同级的所有子元素排序的**，如果不满足条件则不会被选中。

另外一些伪类是按照**选中的相同的标签进行排序号**的，不考虑其他元素。

```css
ul > li:first-of-type {
    // 选中 li 的第一个
}

ul > li:last-of-type {
    // 选中 li 的最后一个
}

ul > li:nth-of-type(2) {
    // 选中 li 的第二个
    // 如果是 n，则会选中所有 li，n 表示 0 -> 正无穷
    // 2n 选中偶数个，或者写 even；2n + 1 奇数个，或者写 odd
}
```

排除某个元素。

```css
ul > li:not(:nth-of-type(3)) {
    // 排除第三个 li 元素
}
```

#### 1.5.2、a元素的伪类

1.   link。正常的链接，没有被访问过的链接。

```css
a:link{
    // 正常的链接，没有被访问过的链接。
}

a:activated {
    // 已访问过的链接，由于隐私原因，只能修改颜色
}

a:hover {
    // 鼠标移上去的样式，不是链接独有，左右元素都可以设置。
}

a:active {
    // 鼠标点击的样式
}
```

#### 1.5.3、伪元素

不存在的元素，表示元素的特殊状态或位置，使用 `::` 来表示。

第一部份就是关于p元素的伪元素。

```css
p::first-letter {
    // 段落的第一个字母
}

p::first-line {
    // 段落的第一行
}

p::selected {
    // 被鼠标选中的部份
}
```

另一部份就是div的伪元素，它不可被鼠标选中，可以通过css向其中添加的内容。

```css
div::before {
    // div 的开始
}

div::after {
    // div 的结束
}
```

before和after是没有内容的，所以添加样式也没有用。只有当里面有内容后，这些样式才有用。

```css
div::before {
    // 往里面添加内容，使用 content 关键字，添加的内容不可被鼠标选中。
    // 所以 before 和 after 只能配合 content 使用
    content: 'hello';
}
```

[css练习小网站](https://flukeout.github.io/)

## 二、基础知识

### 2.1、继承

后代元素可以继承祖先元素的样式，但是部份样式不可以被继承，例如背景相关的，布局相关的。

[css文档参考网站](https://devdocs.io/css/) 里面有各种属性的详细信息。

### 2.2、优先级

每一种选择器都有自己的优先级，当不同选择器选择的样式有冲突时，会按照优先级高的样式生效。

```
1. 内联样式              1000 -> 1,0,0,0
2. id选择器              100  -> 0,1,0,0
3. 类/伪类/属性选择器    10   -> 0,0,1,0
4. 元素选择器             1   -> 0,0,0,1
5. 通用选择器             0   -> 0,0,0,0
6. 继承                   没有优先级
```

1.   选择器按优先级高地显示样式。
2.   交集选择器会将所有优先级相加，并集选择器分别计算优先级
3.   优先级相加再高还是不会超过前一个等级
4.   优先级相同会按照 内联 头部 外部 的优先顺序显示，同一级会按照写下面的优先显示
5.   还有一个最高优先级的会优先显示，就是在样式后面加上 `!important`

### 2.3、像素和百分比

#### 2.3.1、像素

一个像素就是组成屏幕的、发光的一个小点，使用 `px` 表示单位。

```css
div.box1 {
    height: 200px;
    width: 200px;
    background-color: green;
}
```

相同的像素值的元素在不同分辨率的屏幕中显示的大小不同，分辨率越高，显示的越小。

#### 2.3.2、百分比

元素大小还可以通过百分比设置，它是**相对于父元素**的大小设置的。（不太严谨）

```css
div.box2 {
    height: 50%;
    width: 50%;
    background-color: yellow;
}
```

#### 2.3.3、em 和 rem

em 和 rem 也是尺寸单位。

1 em = 1 font-size(16px)。em是根据自身元素的字体大小而变化的，默认元素的 font-size 是16px。

rem 与 em 类似。 1 rem = 1 font-size(16px)。只不过，它是相对于根元素的 font-size 大小而变化的，也就是html元素的font-size。

```css
div.box3 {
    font-size: 20px;
    height: 10em;
    width: 1em;
    background: green;
}

// rem 只与 html 的 font-size 大小有关
html {
    font-size: 20px;
}
div.box3 {
    height: 10 rem;
    width: 1rem;
    background: green;
}
```

### 2.4、颜色

#### 2.4.1、颜色名

使用颜色名指定颜色。缺点：太多不好记，想要的颜色不知道如何表示。

```css
div {
    background: red;
}
```

#### 2.4.2、RGB

使用 三原色（Red，Green， Blue） 表示一种颜色。每一种颜色都有 0 - 255 的取值，共同组合显示一种颜色

```css
div {
    background: rgb(255, 255, 0);
}
```

#### 2.4.3、RGBA

在RGB的基础上增加一个 Alpha 通道，表示颜色的不透明度。取值为 0 - 1。

```css
div {
    // 0.8 可以简写成 .8
    background: rgba(255, 255, 0, .8);
}
```

#### 2.4.4、十六进制

这种方式与 RGB 同理，使用 三原色 表示一种颜色，不过是使用 十六进制 表示每一种三原色，每一种三原色的取值还是 `0 - 255`，用十六进制表示为 `00 - ff`。

```css
div {
    background: #ffff00;
}
```

如果每一种三原色都是有两个相同的字符组成，则可以简写。如上面的可以简写成 `#ff0`。

#### 2.4.5、HSL

HSL 是工业印刷中常使用的表示颜色的方法。

H 色相：选取的颜色，是一个环，从 0 - 360 可以选取不同的颜色。

S 饱和度：颜色的鲜艳程度，`0% - 100%`

L 亮度：颜色的亮度，`0% - 100%` ，0% 就是黑色，100%就是白色。正常显示颜色就是 50%。

## 三、布局

### 3.1、文档流

#### 3.1.1、概念

我们设计**网页是一层一层的**，像PS中的图层一样的，但是用户看到的是从最上层摞下来的。

这些层中**最底下的一层叫作文档流**，文档流是网页的基础，所有**创建的元素默认都是在文档流中**。

元素在主要有两个状态，一是在文档流中，而是不在文档流中。

#### 3.1.2、文档流中元素的特点

**块元素：**

1.   独占一行
2.   默认宽度是父元素的宽度
3.   默认高度是子元素的高度
4.   从上到下一次排列。

**行内元素：**

1.   只占自身大小
2.   默认宽高是内容撑起来的。
3.   排列顺序和平时书写习惯一致。从左到右，一行不够用另起一行，再从左到右。

### 3.2、盒子模型

#### 3.2.1、模型

html中，我们把每个元素都看作一个盒子模型，这个盒子模型是一个矩形区域。

盒子模型包含以下四个重要的部分：

1.   内容区（centent）：放内容（子元素）的部分
2.   内边距（padding）：边框和内容之间的间距
3.   边框（border）：盒子的边框。

上面三个属性决定了盒子的大小。

4.   外边距（margin）：盒子一起摆放的时候，盒子之间的距离。

外边距决定了盒子的位置。

#### 3.2.2、盒子大小

##### 1、content

content 的大小由 `width` 和 `height` 控制。

```css
div {
    width: 100px;
    height:100px;
}
```

##### 2、border

border 至少需要三个元素确定，分别是边框的宽度、颜色、样式。

```css
div {
    width: 100px;
    height:100px;
    // 边框样式
    border-width: 10px;
    border-color: red;
    border-style: solid;
}
```

**宽度：**

四个边可以单独设置边框的宽度、颜色、样式，写不同个数的属性值会有不同的效果。

```css
div {
    /*
    写不同个数的值，对应不同的边框
        4 - 上 右 下 左 (顺时针)
        3 - 上 左右  下
        2 - 上下   左右
        1 - 上下左右
        不写该属性值会有一个默认值，大概是 3px，不同环境有不同效果
    */
    border-width: 10px;
}
```

还可以通过指定边框设定不同边。

```css
div {
    // 使用 top right bottom left 指定不同边的宽度
    border-top-width: 10px;
    border-right-width: 10px;
    border-bottom-width: 10px;
    border-left-width: 10px;
}
```

**颜色：**

颜色的设置与上面的类似，也可以指定不同边的颜色。

如果不写也有一个**默认值，是黑色**。

**样式：**

这个必须设置，**没有默认值**。边框的样式有四种，`solid / dotted / dashed / double`，分别表示实线、点线、虚线、双实线。也可以通过 `4 - 上 右 下 左` 和`border-top-width` 的形式指定不同位置的边框样式。

**三个愿望，一次满足**

同时设置边框的三个样式。

```css
div {
    // 必须按照这个顺序写
    border: 10px red solid;
}
```

还可以指定任意一边的样式

```css
div {
    border-top: 10px red solid;
    border-right: 10px red solid;
    border-bottom: 10px red solid;
    border-left: 10px red solid;
}
```

##### 3、padding

内边距的设置和边框设置类似，也有四条边，但是只有宽度属性。

```css
div {
    /*
        4 - 上 右 下 左 (顺时针)
        3 - 上 左右  下
        2 - 上下   左右
        1 - 上下左右
    */
    padding: 10px 20px 30px 40px;
    // 下面设置等价与上面的
    padding-top: 10px;
    padding-right: 20px;
    padding-bottom: 30px;
    padding-left: 40px;
}
```

1.   内边距会影响盒子的大小。
2.   `background-color`会延伸到内边距上。

可见框：用户可以看到的区域，边框、内边距、内容。

##### 4、margin

margin 的设置方式与padding相同，如果是正值，则会增加该边框的外边距。

1.   设置上边距：正值向下移动，负值向上移动。
2.   设置左边距：正值向右移动，负值向左移动。

上、左边距是移动自身，下、右边距是移动其他元素。

3.   设置下边距：正值其他元素下移，负值其他元素上移（会覆盖自己）
4.   设置右边距：正值其他元素右移（默认设置多少都没有用，因为右边没有元素），负值其他元素左移。

```css
div {
    /*
        4 - 上 右 下 左 (顺时针)
        3 - 上 左右  下
        2 - 上下   左右
        1 - 上下左右
    */
    margin: 10px 20px 30px 40px;
    // 下面设置等价与上面的
    margin-top: 10px;
    margin-right: 20px;
    margin-bottom: 30px;
    margin-left: 40px;
}
```

#### 3.2.3、水平布局

子元素在父元素中的水平布局。需要满足一个等式，就是子元素以下 7 个属性的值之和要等于父元素的 `width`。如果不满足，则称这种情况为过渡布局，浏览器会自动修改一个属性（margin-right）满足这个等式。

子元素这7个属性从左到右分别是：

margin-left

border-left

padding-left

width

padding-right

border-right

margin-right

也就是子元素水平方向的所有属性。

```css
.outer {
    width: 800px;
    height: 200px;
    border: 5px red solid;
}

.innner {
    width: 200px;
    height: 200px;
    background-color: #bfa;
}
```

1.   如果出现了过渡布局，且没有auto属性值的情况，就会默认修改margin-right，使这个等式成立。

2.   子元素这 7 个属性中，有 3 个可以设置为 `auto`，分别是 `margin-left,width,margin-right`如果出现了auto：

     1.   出现一个auto，调整auto，满足等式。

     2.   出现两个auto，其中有width，则会将width撑到最大。

     3.   出现两个auto，没有width，则两边margin各一半，补足满足等式。常用这个方法让子元素居中。

          `margin: 0 auto;`

     4.   出现三个auto，还是会把width撑到最大，margin等于没写。

注意：width不设置样式，默认就是auto，而不是将父元素撑满。是因为auto会将width设置为最大。

如果子元素的width设置超过了父元素的width，则margin-right会设置成负值。

#### 3.2.4、垂直布局

如果不给父元素设置高度，父元素的高度是被子元素的内容撑起来的，永远不会发生溢出。

如果给父元素设置高度后，子元素的内容超过了父元素的高度，就会发生溢出，溢出的处理是交给父元素的 `overflow` 属性处理的。

overflow有四个属性值：

```
overflow:
visible 默认值，让溢出部分可见，正常显示，该是多大显示多大。
hidden 隐藏溢出的部分
scroll 生成水平滚动条和垂直滚动条，拖动滚动条可以查看所有内容
auto 自动生成滚动条，根据需要生成水平或垂直滚动条，或者都生成，都不生成。
```

还有两个属性 `overflow-x/overflow-y` 是分别处理 水平和垂直方向的溢出的，属性值和上面的一样。

1.   父元素的高度是刚好包裹所有子元素的边框。
2.   子元素的margin会出现在父元素外面，那么父元素外的下一个元素是按照父元素和子元素最大的margin排列的。

#### 3.2.5、外边距重叠

**只有垂直方向**的外边距才会发生重叠。

什么是重叠？重叠就是两个元素之间有外边距，元素间距不等于两元素之间边距之和。

-   兄弟元素之间
    -   两个元素的外边距都是正数，取最大的那个为元素的间距
    -   两个元素的外边距一正一负，间距为两者之和
    -   两个元素的外边距都是负的，取绝对值最大的那个。
-   父子元素之间
    -   父子元素的外边距相邻会发生外边距的传递，子元素传给父元素。例如子元素的上边距会传给父元素，父元素就会下移。
    -   这样会影响整体布局，所以必须解决，解决办法就是不让他俩的外边距相邻。例如可以使用一个边框或者一个padding阻挡一下，只要有1px就可以。之后会有完美的解决方案 `top`。

#### 3.2.6、行内元素的盒模型

所有元素都有盒模型，但是行内元素与块元素的盒子模型是不太一样。

**行内元素的盒模型**不支持 `width` 和 `height`，只能有内容撑起。

支持 `padding border margin`，但是垂直方向不会影响其他元素的布局（位置），会将其他元素覆盖。

**将行内元素变成块元素**：

使用 display属性可以设置元素的显示模式，一共有四个属性值。

```
inline 行内元素，只占自身大小
block 块元素，独占一行
inline-block 只占自身大小，但是能设置width 和 height
none 将元素隐藏，不占位置
```

inline-block 集成了inline 和block的优点，也有缺点，不常用。

**visibility** 用来指定元素的显示状态。

```
visible 默认值，可见，正常显示
hidden 隐藏元素，但是在页面中还是占位置，一般不用这个，只用 display 隐藏
```

#### 3.2.7、浏览器自带样式

浏览器个某些元素会带一些默认样式，当我们自定义样式的时候，这些是不需要的，还会阻碍我们写代码，所以我们要清楚这些样式。

一般默认样式都是个元素一些外边距和内边距，还有一些其他的。自己写很麻烦，网上有别人写好的，引入一下css文件就好了。

[Reset CSS](https://meyerweb.com/eric/tools/css/reset/)

#### 3.2.8、盒子大小的计算方式

使用 `box-sizing` 指定可见区（也就是盒子的大小）的方式，也就是指定 width 和 height 的作用。

```css
div {
    // 使用 width 和 height 为内容区的大小，也是默认值
    // 这个时候盒子大小 为 content + padding + border
    box-sizing: content-box;
    
    // width 和 height 指定可见区的大小，也即 content + padding + border 的总和。
    // 这个时候，只要 width 和 height 指定了盒子的大小，里面怎么变化，盒子大小始终是这么大
    box-sizing: border-box;
}
```

#### 3.2.9、轮廓阴影和圆角

##### 3.2.9.1、轮廓

轮廓就是在盒子的外面描一层边，它与border的设置方式是一样的，只不过它不会影响页面的布局，不会计算仅盒子的大小。

```css
div {
    outline: 10px red solid;
}
```

##### 3.2.9.2、阴影

设置盒子的阴影效果，不影响布局。

```css
div {
    box-shadow: 0px 0px 20px black;
}
```

第一个参数 水平偏移量

第二个参数 垂直偏移量

第三个参数 糢糊半径

第四个参数 阴影颜色

##### 3.2.9.3、圆角

设置边框的角为弧形。

```css
// 所有角设置为 半径为 10px 的圆角
border-radius: 10px;

/*
参数个数不同，设置的角不同
4 - 上左 上右 下右 下左
3 - 上左 上右/下左 下右
2 - 上左/下右 上右/下左
1 - 全部
*/
```

还可以单独设置不同的角。

```
border-top-left-radius
border-top-right-radius
border-bottom-right-radius
border-bottom-left-radius
```

还可以设置同一个角的不同半径，那么弧形就是一个椭圆。

```css
border-top-left-radius: 10px 20px;
```

设置水平和垂直方向的半径不同

```css
border-radius: 10px / 20px;
```

### 3.3、浮动

#### 3.3.1、设置浮动

浮动可以让元素脱离文档流 （normal flow），不再受文档流的规则限制，就相当于上浮一层，然后向左或者向右移动。

不受文档流的显示：块级元素不再满足横向的等式，下面的元素在文档流中看不到浮动的元素，会自动向上靠。

如何设置？

```css
div.box1 {
    float: left;
}
```

float 取值有三个：

1.   none 默认值，不浮动
2.   left 浮动后向左移动
3.   right 浮动后向右移动

#### 3.3.2、浮动的特性

1.   脱离文档流，**不受横向等式的约束**。
2.   向上浮动一层，**后面元素看不到浮动元素，自动上移**。
3.   浮动后向左或向右，**不会超过父元素的内容边界**。
4.   一系列左浮动的元素排列规则和文字的排列规则一致。
5.   浮动的元素**不会超过同级的、在文档前面的、浮动的元素** ，这个不会超过指的是水平方向和垂直方向。
6.   如果浮动元素的前面是不浮动的元素，则**浮动元素会被它挡着，不能上移**。

#### 3.3.3、浮动的其他特性

1.   元素变浮动后，后面的元素上移，但是浮动的元素不会盖住后面元素的文字，**文字会环绕这浮动元素**，这也是浮动设计的最初目的。
2.   脱离文档流后，块元素
     1.   块元素不再独占一行
     2.   块元素的宽高由内容撑起来
3.   脱离文档流后，行内元素
     1.   变得和浮动的块元素一样，一样的特点。换句话说，浮动后只有一种元素特点。

## 控制边框和背景

```html
<style type="text/css">
    a {
        border-width: 8px;
        border-color: black;
        border-style: solid;
        border-top-color: white;
        
    }
    p {
        width: 500px;
        height: 600px;
        border: 5px solid red;
        border-top: 10px dashed yellow;
        background-color: blue;
        background-image: url();
    }
</style>
<!-- border-style 有很多属性 -->
```

```html
<style type="text/css">
	body {
        background-attachment: fixed;
		background-image: url();
		backgound-repeat: no-repeat;
        background-size: auto;
        
	}
    
</style>
<!-- background-size:auto/contain/cover -->
```
```html
<style type="text/css">
    p {
        width: 50px;
        height: 50px;
        border: 1px solid black;
        background-color: antiquewhite;
        border-radius: 10px / 15px;
    }
</style>
```
## 文本样式

```html
<style type="text/css">
    p {
        text-align: center;
        direction: ltr;
        letter-spacing: 10px
        word-spacing: 10px
        line-height: 10px
        text-indent: 50px
        text-decoration: underline;
        text-transform: capitalize;  
    }
</style>
<!-- 
	ltr: left to right
	letter-spacing: 字间距
	word-spacing: 单词间距（中文不能用）
	line-height: 行间距
	text-indent: 首行缩进
	text-decoration: 装饰  underline/overline/line-through
-->
```

```html
<style type="text/css">
    p {
        font-family: 微软雅黑;
        font-size: 40px;
        font-style: italic;
        font-variant: small-caps;
        font-weight: 900;
        text-shadow: 10px 10px 5px red
    }
</style>
<!-- 
	font-size: italic/oblique/inherit
	text-shadow: 水平偏移 竖直偏移 模糊程度 阴影颜色
	font-variant: small-caps 小型大写
	font-weight: 100 - 900 的整百
-->

```

## 过渡

```html
<style type="text/css">
    p {
        width: 100px;
        height: 100px;
        background-color: white;
    }
    p:hover {
        width: 200px;
        height: 200px;
        background-color: blue;
        transition-delay: 100ms;<!-- 延迟 -->
        transition-duration: 500ms;<!-- 动画时间 -->
        transition-propety: background-color;<!-- 里面的属性平滑过渡，其他的瞬发 -->
        -weblit-transition-duration: ;
        <!-- -webkit- chrome 和 Safari 浏览器内核支持，安全起见带上 -->
        
    }
</style>
```

## 动画

深入学习过渡

```html
<!DOCTYPE HTML>
<html>
	<head>
        <meta charset="utf-8">
        <title>动画</title>
        <style type="text/css">
            p {
                width: 100px;
                height: 100px;
                background-color: white;
            } 
            p:hover {
                transition-timing-function: ease;
                <!-- 平滑效果速度，其他属性看下面表格 -->
                animation-duration: 500ms;
                animation-delay: 200ms;
                animation-name: suibian;
                animation-iteration-count: infinite;
                <!-- 动画无限执行，一个来回算两次 -->
                animation-direction: alternate;
                <!-- 恢复动画 -->
    		}
            @keyframes suibian {
                from {
                    width: 150px;
                	height: 150px;
                	background-color: orange;
                }
                <!-- 恢复动画的样式 -->
                %50 {
                    
                }
                %75 {
                    
                }
                <!-- 关键帧类似 -->
                to {
                    width: 200px;
                    height: 200px;
                    background-color: blue;
                }
            }
		</style>
	</head>
    <body>
        <p></p>
    </body>
</html>
```

<table>
    <tr>
        <th>属性</th>
        <th>作用</th>
    </tr>
    <tr>
        <td>ease</td>
        <td></td>
    </tr>
    <tr>
        <td>ease-in</td>
        <td>先慢后快</td>
    </tr>
    <tr>
        <td>ease-out</td>
        <td>先快后慢</td>
    </tr>
    <tr>
        <td>ease-in-out</td>
        <td>中间快，两边慢</td>
    </tr>
    <tr>
        <td>liner(默认)</td>
        <td>速度不变</td>
    </tr>
</table>

## 变换

```html
<!DOCTYPE HTML>
<html>
	<head>
        <meta charset="utf-8">
        <title>动画</title>
        <style type="text/css">
            p {
                width: 100px;
                height: 100px;
                background-color: white;
                background-clip: content-box;
                <!-- 背景作用区域 -->
            } 
            p:hover {
                width: 100px;
                height: 100px;
                background-color: white;
                -webkit-transfrom: rotate(45deg);
                <!-- 转动45度，默认顺时针，逆时针加 - 号 -->
                transform: scale(1.5);
                <!-- 缩放  scalex/scaley 缩放x/y -->
                
                transform-origin: top right;
                <!-- 50px 70px -->
    		}
		</style>
	</head>
    <body>
        <p></p>
    </body>
</html>
```

