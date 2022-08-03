# CSS面试题

## **基础题**

### 1.选择器和选择器优先级

| **选择器**     | **格式**      | **优先级权重** |
| -------------- | ------------- | -------------- |
| id选择器       | #id           | 100            |
| 类选择器       | .classname    | 10             |
| 属性选择器     | a[ref=“eee”]  | 10             |
| 伪类选择器     | li:last-child | 10             |
| 标签选择器     | div           | 1              |
| 伪元素选择器   | li::after     | 1              |
| 相邻兄弟选择器 | h1+p          | 0              |
| 子选择器       | ul>li         | 0              |
| 后代选择器     | li a          | 0              |
| 通配符选择器   | *             | 0              |

对于选择器的**优先级**：

- 标签选择器、伪元素选择器：1
- 类选择器、伪类选择器、属性选择器：10
- id 选择器：100
- 内联样式：1000
- !important声明的样式的优先级最高；
- 如果优先级相同，则最后出现的样式生效；
- 继承得到的样式的优先级最低；
- 通用选择器（*）、子选择器（>）和相邻同胞选择器（+）并不在这四个等级中，所以它们的权值都为 0 ；
- 样式表的来源不同时，优先级顺序为：内联样式 > 内部样式 =外部样式 > 浏览器用户自定义样式 > 浏览器默认样式。
- 内部样式 和外部样式优先级是一样的  看谁在后面谁就覆盖前面的

补充：非布局样式：

CSS中，有很多**非布局样式**，这些样式（属性）和与布局无关，包括：

- 字体、字重、颜色、大小、行高
- 背景、边框
- 滚动、换行
- 装饰性属性（粗体、斜体、下划线）等。

文字换行    overflow-wrap：通用的属性。用来说明当一个不能被分开的字符串（单词）太长而不能填充其包裹盒时，为防止其溢出，浏览器是否允许这样的单词**中断换行**

我们可以利用 CSS 实现各种效果，常见的效果属性有：

- box-shadow：盒子的阴影
- text-shadow：文本的阴影
- border-radius  
- background
- clip-path

### 2. CSS中可继承与不可继承属性有哪些

**一、无继承性的属性**

1. **display**：规定元素应该生成的框的类型
2. **文本属性**：

- vertical-align：垂直文本对齐
- text-decoration：规定添加到文本的装饰
- text-shadow：文本阴影效果
- white-space：空白符的处理
- unicode-bidi：设置文本的方向

1. **盒子模型的属性**：width、height、margin、border、padding
2. **背景属性**：background、background-color、background-image、background-repeat、background-position、background-attachment
3. **定位属性**：float、clear、position、top、right、bottom、left、min-width、min-height、max-width、max-height、overflow、clip、z-index
4. **生成内容属性**：content、counter-reset、counter-increment
5. **轮廓样式属性**：outline-style、outline-width、outline-color、outline
6. **页面样式属性**：size、page-break-before、page-break-after
7. **声音样式属性**：pause-before、pause-after、pause、cue-before、cue-after、cue、play-during

**二、有继承性的属性**

1. **字体系列属性**

- font-family：字体系列
- font-weight：字体的粗细
- font-size：字体的大小
- font-style：字体的风格

1. **文本系列属性**

- text-indent：文本缩进
- text-align：文本水平对齐
- line-height：行高
- word-spacing：单词之间的间距
- letter-spacing：中文或者字母之间的间距
- text-transform：控制文本大小写（就是uppercase、lowercase、capitalize这三个）
- color：文本颜色

1. **元素可见性**

- visibility：控制元素显示隐藏

1. **列表布局属性**

- list-style：列表风格，包括list-style-type、list-style-image等

1. **光标属性**

- cursor：光标显示为何种形态

### 2.盒子模型      margin和padding的使用场景     box-sizing

![box](https://s2.loli.net/2022/04/06/3kwqH9GXt7IiglR.png)

盒模型都是由四个部分组成的，分别是margin、border、padding和content。

标准盒模型和IE盒模型的区别在于设置width和height时，所对应的范围不同：

- 标准盒模型的width和height属性的范围只包含了content，
- IE盒模型的width和height属性的范围包含了border、padding和content。

可以通过修改元素的box-sizing属性来改变元素的盒模型：

- `box-sizeing: content-box`表示标准盒模型（默认值）
- `box-sizeing: border-box`表示IE盒模型（怪异盒模型）



### 3.伪类和伪元素的作用(原理)

那如何区分伪元素和伪类，记住两点：

**1. 伪类表示被选择元素的某种状态，例如`:hover`**

**2. 伪元素表示的是被选择元素的某个部分，这个部分看起来像一个独立的元素，但是是"假元素"，只存在于css中，所以叫"伪"的元素，例如`:before`和`:after`**

核心区别在于，是否创造了“新的元素”

#### 伪类

##### 状态类伪类

link、visited、hover、active和focus，分别表示正常状态下的链接、点击后、悬停时、被激活时和聚焦时

##### 结构类伪类

![image-20220406151544117](https://s2.loli.net/2022/04/06/zHUpWxh2Fi7oLJv.png)

```css
p:not(.p1) {
    font-size: .8em;
}
p:first-child {
    font-weight: bold;
}
p:last-child{
    color: rgb(86, 168, 5);
}
p:only-child{
    color: lightcoral;
    background-color: rgb(193, 247, 193);
}
p:nth-child(2n){
    background-color: lightseagreen;
}
p:nth-last-child(3){
    color: #f44;
}
```



##### 表单伪类

![image-20220406151613135](https://s2.loli.net/2022/04/06/ATwKyd6G3hWjzQV.png)



#### 伪元素

`before、after、first-letter、first-line` ，分别表示元素内容的前面、元素内容的后面以及第一个文字和第一行文字

`selection，placeholder`，分别表示被选中部分以及占位的文本

### 4.flex特点和应用

f一、flex弹性的概念：

弹性盒子是一种用于按行或按列布局元素的一维布局方法，元素可以膨胀以填充额外的空间，收缩以适应更小的空间，适用于任何元素上，如果一个元素使用了flex弹性布局（以下都会简称为：flex布局），则会在内部形成[BFC](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FGuide%2FCSS%2FBlock_formatting_context)，flex布局已经得到了所有浏览器的支持，这意味着，现在就能放心，安全的使用这项技术。

![image-20220406152950382](https://s2.loli.net/2022/04/06/CPmyExc9Yj3BwuG.png)

#### 二、主轴与交叉轴：

学习flex布局需要明白”主轴“与”交叉轴“的概念，采用flex布局的元素，称为”容器“ （ flex container），它的所有子元素都是容器的”项目“（flex item），容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做 `main start` ，结束位置叫做 `main end` ；交叉轴的开始位置叫做 `cross start` ，结束位置叫做 `cross end` 。

![image-20220406153012772](https://s2.loli.net/2022/04/06/a9Sq2vI6x7kgPGM.png)

#### 三、容器的属性

容器和项目各有6个属性，见下图

![image-20220406153243934](https://s2.loli.net/2022/04/06/2yatDN43HlBZQjw.png)

![image-20220406153101393](https://s2.loli.net/2022/04/06/no13dj72HLtZSfI.png)

#####  3.1 flex-direction

`flex-direction` 属性决定主轴的方向（即项目的排列方向）

```css
flex-direction: row | row-reverse | column | column-reverse;
```

- `row`（默认值）：主轴为水平方向，起点在左端。
- `row-reverse`：主轴为水平方向，起点在右端。
- `column`：主轴为垂直方向，起点在上沿。
- `column-reverse`：主轴为垂直方向，起点在下沿。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0445e883e66742aea92aa42f675dd9d8~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 3.2 flex-wrap

默认情况下，项目都排在一条线上，无论是否给定宽度，都是不会主动换行的：

```css
.main {
    width: 500px;
    height: 300px;
    background: skyblue;
    display: flex;
}

.main div {
    width: 100px;
    height: 100px;
    background: pink;
    font-size: 20px;
}
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/beb1756393394691bedc34f5089352a3~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

如果需要换行，需要设置flex-wrap

```css
  flex-wrap: nowrap | wrap | wrap-reverse;
```

- `nowrap`（默认值）：不换行。
- `wrap`：换行，第一行在上方。
- `wrap-reverse`：换行，第一行在下方。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b7347d79aed24c40b47f4487b4fb2002~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 3.3 flex-flow

`flex-flow` 属性是 `flex-direction` 属性和 `flex-wrap` 属性的简写形式，默认值为 `row nowrap`
`flex-flow`: `<flex-direction>`|| `<flex-wrap>`;

##### 3.4 justify-content

`justify-content` 属性定义了项目在**主轴上的对齐方式**    主轴上整个列和列项之间距离关系

```css
justify-content: flex-start | flex-end | center | space-around | space-between | space-between;
```

- `flex-start`（默认值）：左对齐
- `flex-end`：右对齐
- `center`： 居中
- `space-around`：每个项目两侧的间隔相等。
- `space-between`：两端对齐，项目之间的间隔都相等。
- `space-evenly`：每个项目的间隔与项目和容器之间的间隔是相等的。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bbc1f587b35248e8ac9e22c2f65e74e1~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 3.5 align-items

`align-items` 属性定义项目在交叉轴上如何对齐   

```css
align-items: flex-start | flex-end | center | baseline | stretch;
```

- `flex-start`：交叉轴的起点对齐。
- `flex-end`：交叉轴的终点对齐。
- `center`：交叉轴的中点对齐。
- `baseline`: 项目的第一行文字的基线对齐。
- `stretch`（默认值）: 如果项目未设置高度或设为auto，将占满整个容器的高度。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8d13305de6a04c67b080c2c342949a13~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 3.6 align-content

`align-content` 属性定义了多根轴线的对齐方式，前提是需要设置flex-wrap: wrap，否则不会有效

```css
align-content: flex-start | flex-end | center | space-between | space-around | stretch;
```

- `flex-start`：与交叉轴的起点对齐。
- `flex-end`：与交叉轴的终点对齐。
- `center`：与交叉轴的中点对齐。
- `space-between`：与交叉轴两端对齐，轴线之间的间隔平均分布。
- `space-around`：每根轴线两侧的间隔都相等。
- `stretch`（默认值）：轴线占满整个交叉轴。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7aef6b1eaf9242cc99ec31823c8606e6~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### 四、项目的属性

##### 4.1 order

`order` 属性定义项目的排列顺序。数值越小，排列越靠前，默认为0，可以是负数。

```css
order: <integer>;
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7ef20acc88f4921a18ff148a8a6f207~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 4.2 flex-grow

`flex-grow` flex容器中剩余空间的多少应该分配给项目，也称为扩展规则。最终的项目的宽度为：自身宽度 + 容器剩余空间分配宽度，flex-grow最大值是1，超过1按照1来扩展

```css
flex-grow: <number>;
/* default 0 */
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/43b5235e6d174f898e8d7b21949be462~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 4.3 flex-shrink

`flex-shrink` 属性指定了 flex 元素的收缩规则。flex 元素仅在默认宽度之和大于容器的时候才会发生收缩，其收缩的大小是依据 flex-shrink 的值，默认值是1

```css
flex-shrink: <number>;
/* default 1 */
```

默认情况下，第一个div宽度是200，第二个div宽度是300，两个相加应该超过父元素的400，但是由于 flex-shrink 都设置为1，将两个div都收缩在父元素中

```css
.item {  width: 400px;    height: 300px;    background: skyblue;    display: flex;    padding: 5px; }
.item div {    height: 100px;    font-size: 20px;}
.item div:nth-child(1) {    flex-shrink: 1;    width: 200px;    background: pink;}
.item div:nth-child(2) {    flex-shrink: 1;    width: 300px;    background: cadetblue;}
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3c23dd6853934181a0aefbd7c87982ef~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bcbefb1a402a449587595d4cd5aa0cee~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/da115bcb70d94f16b43061db78aa7eb1~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

如果将第一个div设置为 `flex-shrink: 0; `不收缩，则按容器实际宽度展示

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a8da3cba1e2842c0b7eaf30905a3f645~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

那收缩后的子项宽度是怎么样计算的呢？实际上有一个公示：

1. `(200+300)`所有子项的宽度的和 - `(400)`容器的宽度 = `(100)`
2. 第一个子项的宽度占比：`2/5`，第二个子项的宽度占比：`3/5`
3. 则第一个子项的的宽度为：`200` - `2/5 * 100` = 160，第二个子项的宽度为：`300` - `3/5 * 100` = 240

##### 4.4 flex-basis

`flex-basis` 指定了子项在容器主轴方向上的初始大小，优先级高于自身的宽度width

```css
flex-basis: 0 | 100% | auto | <length>
```

宽度是200，但是由于设置了 `flex-basis: 300px; `，所以子项最终宽度是大于自身设置的宽度

```css
.item {  width: 400px;   height: 300px;  background: skyblue;  display: flex;    padding: 5px;}
.item div {  height: 100px;  font-size: 20px;}
.item div {  width: 200px;  flex-basis: 300px;  background: pink;}
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/95a2ff35cf2a49d5b5fe8b2e793436db~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 4.5 flex

`flex` 属性是 `flex-grow` , `flex-shrink` 和 `flex-basis` 的简写，默认值为 `0 1 auto` 。后两个属性可选。

```css
flex: none | [ <'flex-grow'><'flex-shrink'>? || <'flex-basis'>]
```

##### 4.6 align-self

`align-self` 属性允许单个项目有与其他项目不一样的对齐方式，可覆盖 `align-items` 属性。默认值为 `auto` ，表示继承父元素的 `align-items` 属性，如果没有父元素，则等同于 `stretch` 。

```css
align-self: auto | flex-start | flex-end | center | baseline | stretch;

.item {    
    width: 400px;
    height: 300px; 
    background: skyblue;
    display: flex;
    padding: 5px;
}
.item div {    height: 100px;    font-size: 20px;}
.item div {    width: 200px;    flex-basis: 300px;}
.item div:nth-child(1) {    background: pink;    align-self: flex-start;}  
.item div:nth-child(2) {    background: violet;    align-self: center;}  
.item div:nth-child(3) {    background: greenyellow;    align-self: flex-end;}  
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b48825d152e143fa8f633854ac0d2a28~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

flex属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写, 默认值是`0 1 auto`。
`flex-grow`是如果有剩余空间，是否扩大，`0`为不扩大
`flex-shrink`是如果剩余空间不够，是否缩小，`1`为缩小
`flex-basis`为项目本身的大小，默认值是`auto`

#### 简写属性

下面来分别讲讲 `flex:1` `flex:auto`, `flex:0`, `flex:none`的区别

##### flex: 1

`flex: 1`, 对应的是`1 1 0%`, 相当于可扩大，可缩小，`flex-basis`为0%

##### flex: auto

`flex: auto`, 对应的是`1 1 auto`, 相当于可扩大，可缩小，`flex-basis`为`auto`

`flex: 1` 不管内容多少，一般都是**平分空间**，空间大小都一致
而`flex: auto`是根据内容的大小来分，不是均分的（除非内容都是一样，才均分）

##### flex: 0


`flex: 0`, 对应的是`0 1 0%`, 相当于不可扩大，可缩小，`flex-basis`为`0%

##### flex: none

`flex: none`, 对应的是`0 0 auto`, 相当于不可扩大，不可缩小，`flex-basis`为`auto`

`flex: 0` 不可扩大，可缩小,表现形式为最小内容宽度, 
而`flex: none` 不可扩大，不可缩小，内容本身的宽度是多少就是多少

### 5.css中哪些属性是可继承的和不可继承的

#### **一、无继承性的属性**

1.  **display**：规定元素应该生成的框的类型
2.  **文本属性**：

-   vertical-align：垂直文本对齐
-   text-decoration：规定添加到文本的装饰
-   text-shadow：文本阴影效果
-   white-space：空白符的处理
-   unicode-bidi：设置文本的方向

1.  **盒子模型的属性**：width、height、margin、border、padding
2.  **背景属性**：background、background-color、background-image、background-repeat、background-position、background-attachment
3.  **定位属性**：float、clear、position、top、right、bottom、left、min-width、min-height、max-width、max-height、overflow、clip、z-index
4.  **生成内容属性**：content、counter-reset、counter-increment
5.  **轮廓样式属性**：outline-style、outline-width、outline-color、outline
6.  **页面样式属性**：size、page-break-before、page-break-after
7.  **声音样式属性**：pause-before、pause-after、pause、cue-before、cue-after、cue、play-during

#### **二、有继承性的属性**

1.  **字体系列属性**

-   font-family：字体系列
-   font-weight：字体的粗细
-   font-size：字体的大小
-   font-style：字体的风格

1.  **文本系列属性**

-   text-indent：文本缩进
-   text-align：文本水平对齐
-   line-height：行高
-   word-spacing：单词之间的间距
-   letter-spacing：中文或者字母之间的间距
-   text-transform：控制文本大小写（就是uppercase、lowercase、capitalize这三个）
-   color：文本颜色

1.  **元素可见性**

-   visibility：控制元素显示隐藏

1.  **列表布局属性**

-   list-style：列表风格，包括list-style-type、list-style-image等

1.  **光标属性**

-   cursor：光标显示为何种形态

### 6.display的属性

以下是一些关于display比较常用的属性值：

|     **值**     | **描述**                                                     |
| :------------: | ------------------------------------------------------------ |
|    **none**    | **元素不会显示**                                             |
|   **block**    | **此元素将显示为块级元素，此元素前后会带有换行符。**         |
|   **inline**   | **默认。此元素会被显示为内联元素，元素前后没有换行符。**     |
| **line-block** | 行内块元素。（CSS2.1 新增的值）[IE6/7不支持]                 |
|   list-item    | 此元素会作为列表显示。                                       |
|  inline-table  | 此元素会作为内联表格来显示（类似 table），表格前后没有换行符。 |
|     table      | 此元素会作为块级表格来显示（类似 table），表格前后带有换行符。 |
|   table-row    | 此元素会作为一个表格行显示（类似 tr）。                      |
|   table-cell   | 此元素会作为一个表格单元格显示（类似 td 和 th）.             |
|    inherit     | 规定应该从父元素继承 display 属性的值。                      |

其中我们在前端开发中比较常用的属性值一般是none、block、inline、inline-block。

#### display:none

1. 将元素与其子元素从普通文档流中移除。这时文档的渲染就像元素从来没有存在过一样，也就是说它所占据的空间消失了。元素的内容也会消失。

#### display:block

1. **block元素会独占一行，多个block元素会各自新起一行**。默认情况下，block元素宽度自动填满其父元素宽度；
2. block元素可以设置margin和padding属性；
3. block元素可以设置width、height属性。
4. 块级元素即使**设置了宽度，仍然是独占一行**。块级元素在设置宽度的情况下，是通过自定义margin-right来自动填满一行，这个时候你设置margin-right是无效的；块级元素在没有设置宽度的时候，margin-right会生效，块级元素的width通过自定义在自动填满一行

#### display:inline

1. inline元素**不会独占一行**，**多个相邻的行内元素会排列在同一行里**，直到一行排列不下，才会新换一行，其宽度随元素的内容而变化；
2. inline元素**设置width、height属性无效**；
3. inline元素的margin和padding属性，**水平方向的padding-left、padding-right、margin-left、margin-right都产生边距效果**；**但竖直方向的padding-top、padding-bottom、margin-top、margin-bottom不会产生边距效果。**

**`vertical-align`** 用来指定行内元素（inline）或表格单元格（table-cell）元素的垂直对齐方式。

注意 `vertical-align` 只对行内元素、行内块元素和表格单元格元素生效

#### display:inline-block

1. 将对象呈现为inline对象，但是对象的内容作为block对象呈现，之后的内联对象会被排列在同一行内。结合了inline和block特性，既能设置宽高也不会换行
2. 行内块级元素起始就是行内元素设置宽高可以生效**width、height、margin、padding设置都会生效**。

#### inline-block 引起的空白间隙问题

`问题原因`:元素被当成行内元素排版的时候，元素之间的空白符（空格、回车换行等）都会被浏览器处理，根据`white-space`的处理方式（默认是normal，合并多余空白），原来HTML代码中的`回车换行`被转成一个`空白符`，所以元素之间就出现了`空隙`。这些元素之间的间距会随着不同环境而变化（字族、字体大小、white-space都会影响）

`block`元素和`inline`也会有这样的效果

##### 1.使用浮动

我们可以通过给子元素添加浮动来达到消除间距的效果。

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .box1{
      display: inline-block;
      width: 100px;
      height: 100px;
      background: pink;
      float: left;
    }
    .box2{
      display: inline-block;
      width: 100px;
      height: 100px;
      background: greenyellow;
    }
  </style>
</head>
<body>
  <div class="box1">111</div>
  <div class="box2">111</div>
</body>
</html>

```

![image-20220406151714541](https://s2.loli.net/2022/04/06/n2wl7xAErtgSTbs.png)

缺点:

-   浮动会造成高度坍塌等不好操作
-   特定场合还需要去清除浮动


不推荐


##### 2.清除行内块元素之间的空格和换行符

上面介绍到两个行内块元素之间的空格和换行符都会被浏览器所处理,那么我们就消除。 `不建议`

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .box1{
      display: inline-block;
      width: 100px;
      height: 100px;
      background: pink;
    }
    .box2{
      display: inline-block;
      width: 100px;
      height: 100px;
      background: greenyellow;
    }
  </style>
</head>
<body>
  <div class="box1">111</div><div class="box2">111</div>
</body>
</html>

```

![image-20220406151732352](https://s2.loli.net/2022/04/06/748k15HnIChdNMX.png)

缺点:

-   代码不美观,看起来很臃肿
-   编译器可能有自动格式化会强制换行


不推荐

##### 3.父元素设置font-size:0

前面我们介绍到间距其实是由于换行符等被转义,是由`font-size`进行控制的,我们可以将父元素的`font-size`设置为0

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    body{
      font-size: 0px;
    }
    .box1{
      display: inline-block;
      width: 100px;
      height: 100px;
      background: pink;
    }
    .box2{
      display: inline-block;
      width: 100px;
      height: 100px;
      background: greenyellow;
    }
  </style>
</head>
<body>
  <div class="box1">111</div>
  <div class="box2">111</div>
</body>
</html>
```

![image-20220406151759347](https://s2.loli.net/2022/04/06/dZc3bKWjheXPnIx.png)

可以发现我们这里字体消失了

原因:由于`font-size`是可继承的,所以子元素的字体大小也变为了0,所以我们需要给子元素加上`font-size`。

缺点:

-   由于`font-size`是可继承的,所以子元素还需要单独设置`font-size`


推荐


##### 4.父元素设置word-spacing

通过父元素设置`word-spacing`为负值（负值可无限大，负值超出不会造成影响）,达到消除间距的作用

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    body{
      word-spacing: -5px;
    }
    .box1{
      display: inline-block;
      width: 100px;
      height: 100px;
      background: pink;
    }
    .box2{
      display: inline-block;
      width: 100px;
      height: 100px;
      background: greenyellow;
    }
  </style>
</head>
<body>
  <div class="box1">111</div>
  <div class="box2">111</div>
</body>
</html>
```

![](https://s2.loli.net/2022/04/06/dZc3bKWjheXPnIx.png)

缺点:

-   可能会影响父元素里的其他元素(可单独给需要消除的包裹一层div)



### 7.隐藏元素的方法有哪些？ display:none和visibility:hidden区别

- **display: none：渲染树不会包含该渲染对象，因此该元素不会在页面中占据位置，也不会响应绑定的监听事件。**
- **visibility: hidden：元素在页面中仍占据空间，但是不会响应绑定的监听事件。**
- **opacity: 0**：将元素的透明度设置为 0，以此来实现元素的隐藏。元素在页面中仍然占据空间，并且能够响应元素绑定的监听事件。
- **position: absolute**：通过使用绝对定位将元素移除可视区域内，以此来实现元素的隐藏。
- **z-index: 负值**：来使其他元素遮盖住该元素，以此来实现隐藏。
- **clip/clip-path** ：使用元素裁剪的方法来实现元素的隐藏，这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。
- **transform: scale(0,0)**：将元素缩放为 0，来实现元素的隐藏。这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。

### 8.link和@import区别

两者都是外部引用CSS的方式，它们的区别如下：

- link是XHTML标签，除了加载CSS外，还可以定义RSS等其他事务；@import属于CSS范畴，只能加载CSS。
- link引用CSS时，在页面载入时同时加载；@import需要页面网页完全载入以后加载。
- link是XHTML标签，无兼容问题；@import是在CSS2.1提出的，低版本的浏览器不支持。
- link支持使用Javascript控制DOM去改变样式；而@import不支持。

### 8. display:none与visibility:hidden的区别

这两个属性都是让元素隐藏，不可见。**两者区别如下：**

（1）**在渲染树中**

- `display:none`会让元素完全从渲染树中消失，渲染时不会占据任何空间；
- `visibility:hidden`不会让元素从渲染树中消失，渲染的元素还会占据相应的空间，只是内容不可见。

（2）**是否是继承属性**

- `display:none`是非继承属性，子孙节点会随着父节点从渲染树消失，通过修改子孙节点的属性也无法显示；
- `visibility:hidden`是继承属性，子孙节点消失是由于继承了`hidden`，通过设置`visibility:visible`可以让子孙节点显示； （3）修改常规文档流中元素的 `display` 通常会造成文档的重排，但是修改`visibility`属性只会造成本元素的重绘；

（4）如果使用读屏器，设置为`display:none`的内容不会被读取，设置为`visibility:hidden`的内容会被读取。

### 9.transition和animation的区别

- **transition是过度属性**，强调过度，它的实现需要触发一个事件（比如鼠标移动上去，焦点，点击等）才执行动画。它类似于flash的补间动画，设置一个开始关键帧，一个结束关键帧。
- **animation是动画属性**，它的实现不需要触发事件，设定好时间之后可以自己执行，且可以循环一个动画。它也类似于flash的补间动画，但是它可以设置多个关键帧（用@keyframe定义）完成动画。

#### transition 语法

| 值                           | 描述                                       |
| ---------------------------- | ------------------------------------------ |
| *transition-duration*        | transition效果需要指定多少秒或毫秒才能完成 |
| *transition-property*        | 指定CSS属性的name，transition效果          |
| *transition-timing-function* | 指定transition效果的转速曲线               |
| *transition-delay*           | 定义transition效果开始的时候               |



>   transition翻译成中文是过渡的意思，顾名思义，它就是专门做过渡动画的，比如一些放大缩小，隐藏显示等，下面我们一起来学习一下他的语法。

#### transition-duration：transition效果需要指定多少秒或毫秒才能完成

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/16/16dd2333abe6bdf1~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

```css
div{
    width:100px;
    height:100px;
    border-radius: 50%;
    background:#f40;
    transition-duration:1s;
}
div:hover{
    height:150px;	
    width:150px;
}
```

这是transition最基本的用法，`transition-duration`为动画执行所需的时间，这段代码的意思就是鼠标移入，div的宽高就会都变成`150px`

#### transition-property：指定CSS属性的name，transition效果

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/16/16dd2341c88707d0~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

```css
div{
    width:100px;
    height:100px;
    border-radius: 50%;
    background:#f40;
    transition-duration:1s;
    transition-property:width;
}
div:hover{
    height:150px;	
    width:150px;
}
```

这里`transition-property`值仅为width，意思是只给`width`加动画，所以会呈现这种效果，同样如果换成了`height`，那么将会是变高才有动画。

我们发现，第一个案例我们并没有写`transition-property`，但是案例中`width`和`height`属性是同时变化的，那是因为`transition-property`的默认值为`all`，只要不写这个属性，那就是全部变化都会执行动画。

#### transition-timing-function：指定transition效果的转速曲线

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/16/16dd23475b675c82~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

```css
div{
    width:100px;
    height:50px;
    background:#f40;
    transition-duration:2s;
    transition-timing-function:ease-in-out;
}
div:hover{
    width:250px;
}
```

`transition-timing-function`的作用就是改变动画在每一帧的快慢。这里`transition-timing-function`仅展示值为`ease-in-out`的动画效果，可以理解为`慢-快-慢`。其他的不做展示，可以参考下表进行理解。

| 值                            | 描述                                                         | 速度     |
| ----------------------------- | ------------------------------------------------------------ | -------- |
| linear（默认属性）            | 规定以相同速度开始至结束的过渡效果（等于 cubic-bezier(0,0,1,1)）。 | 匀速     |
| ease                          | 规定慢速开始，然后变快，然后慢速结束的过渡效果（cubic-bezier(0.25,0.1,0.25,1)）。 | 快-慢-慢 |
| ease-in                       | 规定以慢速开始的过渡效果（等于 cubic-bezier(0.42,0,1,1)）。  | 快-快    |
| ease-out                      | 规定以慢速结束的过渡效果（等于 cubic-bezier(0,0,0.58,1)）。  | 慢-慢    |
| ease-in-out                   | 规定以慢速开始和结束的过渡效果（等于 cubic-bezier(0.42,0,0.58,1)）。 | 慢-快-慢 |
| cubic-bezier(*n*,*n*,*n*,*n*) | 在 cubic-bezier 函数中定义自己的值。可能的值是 0 至 1 之间的数值。 | 自定义   |



#### transition-delay：定义transition效果开始的时候

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/16/16dd2355b0fb9120~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

```css
div{
    width:100px;
    height:100px;
    border-radius: 50%;
    background:#f40;
    transition-duration:1s;
    transition-delay:1s;
}
div:hover{
    height:150px;	
    width:150px;
}
```

这里`transition-delay`的值为`1s`，意思是动画将在延迟一秒后执行。

------

今天我们一共学习了四个属性值，他们都是属于`transition`属性的，这里给出属性值在`transition`中的简写方式。

>   transition: property duration timing-function delay;

```css
div{
    transition:all 1s ease-in-out 2s;
}
```

那么这里的意思就是所有属性都加入持续一秒，缓进缓出的动画，并在两秒钟后开始执行。

#### 结论

经过以上的学习，相信你已经初步了解了`transition`的用法，`transition`用来过渡再好不过了，但是仅靠它做不了一些炫酷的动画，我将在接下来的文章继续为你讲解`animation（动画）`、`translate（移动）`以及`transform（变形）`

#### animation 语法

| 值                          | 描述                                                         |
| --------------------------- | ------------------------------------------------------------ |
| *@keyframes*                | 定义一个动画,@keyframes定义的动画名称用来被animation-name所使用 |
| *animation-name*            | 检索或设置对象所应用的动画名称 ,必须与规则@keyframes配合使用，因为动画名称由@keyframes定义 |
| *animation-duration*        | 检索或设置对象动画的持续时间                                 |
| *animation-timing-function* | 检索或设置对象动画的过渡类型                                 |
| *animation-delay*           | 检索或设置对象动画的延迟时间                                 |
| *animation-iteration-count* | 检索或设置对象动画的循环次数                                 |
| *animation-direction*       | 检索或设置对象动画在循环中是否反向运动                       |
| *animation-play-state*      | 检索或设置对象动画的状态                                     |



>   animation翻译成中文是动画的意思，熟练运用之后你可以用它来做各种各样炫酷的动画。

#### @keyframes：定义一个动画，定义的动画名称用来被animation-name所使用。



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/1/16/16fae589a28024ba~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
div{
    width:50px;
    height:50px;
    background:#f40;
    border-radius:50%;
    animation:mymove 2s;
}

@keyframes mymove{
    0%   {width:50px;height:50px;}	
    50%   {width:100px;height:100px;}	
    100%   {width:50px;height:50px;}
}
```

@keyframes主要是做关键帧动画的，每个@keyframes后面都要跟一个名字，事例中使用了`mymove`作为帧动画的名字，然后可以在样式内对关键帧添加样式，然后根据关键帧 `@keyframes`就能自动形成流畅的动画了。

#### animation-name：检索或设置对象所应用的动画名称 ,必须与规则@keyframes配合使用，因为动画名称由@keyframes定义



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/1/16/16fae589a28024ba~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
div{
    width:50px;
    height:50px;
    background:#f40;
    border-radius:50%;
    animation-name:mymove;
    animation-duration:2s;
}

@keyframes mymove{
    0%   {width:50px;height:50px;}	
    50%   {width:100px;height:100px;}	
    100%   {width:50px;height:50px;}
}
```

在`animation-name`使用之前，我们已经定义了一个名为`mymove`的帧动画，这里把帧动画的名字作为了animation-name的值，含义是当前元素将执行所设置的帧动画。

#### animation-duration：检索或设置对象动画的持续时间

继续看上一个案例，仅仅有帧动画和需要执行的动画名称是不足以形成动画的，我们还需要设置一个动画执行所需要的时间，这里就用到了`animation-duration`属性，所以上一案例所展示的时间为两秒钟执行一次。

#### animation-timing-function：检索或设置对象动画的过渡类型



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/1/16/16fae58b40a77f97~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
div{
    width:100px;
    height:50px;
    background:#f40;
    position:relative;
    animation-name:mymove;
    animation-duration:3s;
    animation-timing-function:ease-in-out;
}
@keyframes mymove{
    0% {left:0px;}
    100% {left:300px;}
}
```

`animation-timing-function`的作用就是改变动画在每一帧的快慢。这里`transition-timing-function`仅展示值为`ease-in-out`的动画效果，可以理解为`慢-快-慢`。其他的不做展示，可以参考下表进行理解。

| 值                    | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| linear                | 动画从头到尾的速度是相同的。                                 |
| ease                  | 默认。动画以低速开始，然后加快，在结束前变慢。               |
| ease-in               | 动画以低速开始。                                             |
| ease-out              | 动画以低速结束。                                             |
| ease-in-out           | 动画以低速开始和结束。                                       |
| cubic-bezier(n,n,n,n) | 在 cubic-bezier 函数中自己的值。可能的值是从 0 到 1 的数值。 |



#### animation-delay：检索或设置对象动画的延迟时间



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/1/16/16fae58c3693d376~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
div{
    width:50px;
    height:50px;
    background:#f40;
    border-radius:50%;
    animation-name:mymove;
    animation-duration:2s;
    animation-delay:2s;
}

@keyframes mymove{
    0%   {width:50px;height:50px;}	
    50%   {width:100px;height:100px;}	
    100%   {width:50px;height:50px;}
}
```

这里`animation-delay`的值为`2s`，意思是动画将在延迟两秒秒后延迟执行。

#### animation-iteration-count：检索或设置对象动画的循环次数



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/1/16/16fae58d52c68cfc~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
div{
    width:50px;
    height:50px;
    background:#f40;
    border-radius:50%;
    animation-name:mymove;
    animation-duration:2s;
    animation-iteration-count:infinite;
}

@keyframes mymove{
    0%   {width:50px;height:50px;}	
    50%   {width:100px;height:100px;}	
    100%   {width:50px;height:50px;}
}
```

这里`animation-iteration-count`的值为`infinite`，意思是动画将会无限次的执行，这也就达到了循环的效果，当然你还可以给它具体的数值，当执行你设置的次数后它会自动停止。

#### animation-direction：检索或设置对象动画在循环中是否反向运动



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/1/16/16fae58ee41520d5~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
div{
    width:100px;
    height:50px;
    background:#f40;
    position:relative;
    animation-name:mymove;
    animation-duration:2s;
    animation-iteration-count:infinite;
    animation-direction:alternate;
}

@keyframes mymove{
    0% {left:0px;}
    100% {left:300px;}
}
```

这里`animation-direction`的值为`alternate`，代表动画将会来回的反复执行，他还有其它属性，在下表给出供小伙伴们自己尝试。

| 值                | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| normal            | 默认值。动画按正常播放。                                     |
| reverse           | 动画反向播放。                                               |
| alternate         | 动画在奇数次（1、3、5...）正向播放，在偶数次（2、4、6...）反向播放。 |
| alternate-reverse | 动画在奇数次（1、3、5...）反向播放，在偶数次（2、4、6...）正向播放。 |
| initial           | 设置该属性为它的默认值。                                     |
| inherit           | 从父元素继承该属性。                                         |



#### animation-play-state：检索或设置对象动画的状态



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/1/16/16fae58ed4a21cc1~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
<style>
div{
    width:50px;
    height:50px;
    background:#f40;
    border-radius:50%;
    animation-name:mymove;
    animation-duration:2s;
    animation-iteration-count:infinite;
}
@keyframes mymove{
    0%   {width:50px;height:50px;}	
    50%   {width:100px;height:100px;}	
    100%   {width:50px;height:50px;}
}
</style>
<body>
    <button onclick="pause()">暂停</button>
    <button onclick="run()">恢复</button>
    <div></div>
</body>

function pause(){
    document.querySelector('div').style.animationPlayState="paused"
}
function run(){
    document.querySelector('div').style.animationPlayState="running"
}
```

`animation-play-state`的默认值为`running`，就是动画执行的意思，在实际应用中我们经常使用js来操作这个属性，从而控制动画的播放和暂停。

------

今天我们一共学习了八个属性值，他们都是属于`animation`属性的，这里给出属性值在`animation`中的简写方式。

>   animation: name duration timing-function delay iteration-count direction play-state;

```css
div{
    animation:mymove 2s ease-in-out 3s infinite alternate running;
}
```

那么这里的意思就是`mymove`动画将在三秒钟后开始，以两秒一个循环慢-快-慢方式，进行动画的展示，并且每次动画过后都会向相反方向执行动画。

#### transform 语法

| 值                    | 描述                                  |
| --------------------- | ------------------------------------- |
| none                  | 定义不进行转换。                      |
| translate(x,y)        | 定义 2D 转换。                        |
| translate3d(x,y,z)    | 定义 3D 转换。                        |
| translateX(x)         | 定义转换，只是用 X 轴的值。           |
| translateY(y)         | 定义转换，只是用 Y 轴的值。           |
| translateZ(z)         | 定义 3D 转换，只是用 Z 轴的值。       |
| scale(x[,y]?)         | 定义 2D 缩放转换。                    |
| scale3d(x,y,z)        | 定义 3D 缩放转换。                    |
| scaleX(x)             | 通过设置 X 轴的值来定义缩放转换。     |
| scaleY(y)             | 通过设置 Y 轴的值来定义缩放转换。     |
| scaleZ(z)             | 通过设置 Z 轴的值来定义 3D 缩放转换。 |
| rotate(angle)         | 定义 2D 旋转，在参数中规定角度。      |
| rotate3d(x,y,z,angle) | 定义 3D 旋转。                        |
| rotateX(angle)        | 定义沿着 X 轴的 3D 旋转。             |
| rotateY(angle)        | 定义沿着 Y 轴的 3D 旋转。             |
| rotateZ(angle)        | 定义沿着 Z 轴的 3D 旋转。             |
| skew(x-angle,y-angle) | 定义沿着 X 和 Y 轴的 2D 倾斜转换。    |
| skewX(angle)          | 定义沿着 X 轴的 2D 倾斜转换。         |
| skewY(angle)          | 定义沿着 Y 轴的 2D 倾斜转换。         |
| perspective(n)        | 为 3D 转换元素定义透视视图.           |



>   `transform`字面上就是变形，改变的意思。看起来他有很多属性，其实我们把常用的总结起来就是下面四个属性。

-   rotate(旋转)
-   skew(扭曲)
-   scale(缩放)
-   translate(移动)

#### rotate 旋转



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/29/16e16d92863837e3~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
<style>
img{
    margin-left: 50px;
    width:50px;
    height:50px;
    border-radius:50%;
}
@keyframes rotate{
    0%   {transform:rotate(0deg);}	
    100%   {transform:rotate(360deg);}
}
@keyframes rotateX{
    0%   {transform:rotateX(0deg);}	
    100%   {transform:rotateX(360deg);}
}
@keyframes rotateY{
    0%   {transform:rotateY(0deg);}	
    100%   {transform:rotateY(360deg);}
}

.rotate{
    animation:rotate 2s infinite linear;
}
.rotateX{
    animation:rotateX 2s infinite linear;
}
.rotateY{
    animation:rotateY 2s infinite linear;
}
</style>
<body>
    <img src="./y.png" alt="" class="rotate">
    <img src="./y.png" alt="" class="rotateX">
    <img src="./y.png" alt="" class="rotateY">
</body>
```

这里一共有三个属性的展示`rotate`，`rotateX`，`rotateY`。分别代表在平面上根据指定角度进行旋转、沿着X轴进行指定角度的旋转、沿着Y轴进行制定角度的旋转。

#### translate 移动



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/29/16e16e2b100c1454~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
<style>
img{
    margin-left: 50px;
    width:50px;
    height:50px;
    border-radius:50%;
}
@keyframes translate{
    0%   {transform:translate(0px,0px);}	
    100%   {transform:translate(100px,100px);}
}
@keyframes translateX{
    0%   {transform:translateX(0px);}	
    100%   {transform:translateX(100px);}
}
@keyframes translateY{
    0%   {transform:translateY(0px);}	
    100%   {transform:translateY(100px);}
}

.translate{
    animation:translate 2s infinite linear;
}
.translateX{
    animation:translateX 2s infinite linear;
}
.translateY{
    animation:translateY 2s infinite linear;
}
</style>
<body>
    <img src="./y.png" alt="" class="translate">
    <img src="./y.png" alt="" class="translateX">
    <img src="./y.png" alt="" class="translateY">
</body>
```

这里一共有三个属性的展示`translate(x,y)`，`translateX(x)`，`translateY(Y)`。分别代表水平方向和垂直方向同时移动、仅水平方向移动、仅垂直方向移动。

#### scale 缩放



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/29/16e16eb9e2a38fc0~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
<style>
img{
    margin-left: 50px;
    width:50px;
    height:50px;
    border-radius:50%;
}
@keyframes scale{
    0%   {transform:scale(0.1,0.1);}	
    100%   {transform:scale(1,1);}
}
@keyframes scaleX{
    0%   {transform:scaleX(0.1);}	
    100%   {transform:scaleX(1);}
}
@keyframes scaleY{
    0%   {transform:scaleY(0.1);}	
    100%   {transform:scaleY(1);}
}

.scale{
    animation:scale 2s infinite linear;
}
.scaleX{
    animation:scaleX 2s infinite linear;
}
.scaleY{
    animation:scaleY 2s infinite linear;
}

</style>
<body>
    <img src="./y.png" alt="" class="scale">
    <img src="./y.png" alt="" class="scaleX">
    <img src="./y.png" alt="" class="scaleY">
</body>
```

这里一共有三个属性的展示`scale(x,y)`，`scaleX(x)`，`scaleY(Y)`。分别代表水平方向和垂直方向同时缩放、仅水平方向缩放、仅垂直方向缩放。但它们具有相同的缩放中心点和基数，其中心点就是元素的中心位置，缩放基数为1，如果其值大于1元素就放大，反之其值小于1，元素缩小。

#### skew 扭曲



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/30/16e1aad417b03617~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



```css
<style>
img{
    margin-left: 50px;
    width:50px;
    height:50px;
    /* border-radius:50%; */
}
@keyframes skew{
    0%   {transform:skew(0deg,0deg);}	
    100%   {transform:skew(25deg,25deg);}
}
@keyframes skewX{
    0%   {transform:skewX(0deg);}	
    100%   {transform:skewX(25deg);}
}
@keyframes skewY{
    0%   {transform:skewY(0deg);}	
    100%   {transform:skewY(25deg);}
}

.skew{
    animation:skew 2s infinite linear;
}
.skewX{
    animation:skewX 2s infinite linear;
}
.skewY{
    animation:skewY 2s infinite linear;
}
</style>
<body>
    <img src="./y.png" alt="" class="skew">
    <img src="./y.png" alt="" class="skewX">
    <img src="./y.png" alt="" class="skewY">
</body>
```

这里一共有三个属性的展示`skew(x,y)`，`skew(x)`，`skewY(Y)`。分别代表水平方向和垂直方向同时扭曲、仅水平方向扭曲、仅垂直方向扭曲。



#### 结论

经过以上的学习，相信你已经初步了解了`animation`的用法，随着你对`animation`的深入理解，是可以做一些有创造性的动画的，你可以看看自己之前用js写的各种动画，尝试着用我们两篇文章所学的内容进行修改，相信你一定会对这两个属性有更深的理解。

但是现在我们只是学会了过渡和动画，我们现在还不能对图形进行一系列的不规则操作，而`transform（变形）`就是来操作改变成特殊图形的，我将在接下来的文章继续为你讲解`translate（移动）`以及`transform（变形）`

### 11.对requestAnimationframe的理解

实现动画效果的方法比较多，Javascript 中可以通过定时器 setTimeout 来实现，CSS3 中可以使用 transition 和 animation 来实现，HTML5 中的 canvas 也可以实现。除此之外，HTML5 提供一个专门用于请求动画的API，那就是 requestAnimationFrame，顾名思义就是**请求动画帧**。

MDN对该方法的描述：

> window.requestAnimationFrame() 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。

**语法：** `window.requestAnimationFrame(callback);`  其中，callback是**下一次重绘之前更新动画帧所调用的函数**(即上面所说的回调函数)。该回调函数会被传入DOMHighResTimeStamp参数，它表示requestAnimationFrame() 开始去执行回调函数的时刻。该方法属于**宏任务**，所以会在执行完微任务之后再去执行。

**取消动画：** 使用cancelAnimationFrame()来取消执行动画，该方法接收一个参数——requestAnimationFrame默认返回的id，只需要传入这个id就可以取消动画了。

**优势：**

- **CPU节能**：使用SetTinterval 实现的动画，当页面被隐藏或最小化时，SetTinterval 仍然在后台执行动画任务，由于此时页面处于不可见或不可用状态，刷新动画是没有意义的，完全是浪费CPU资源。而RequestAnimationFrame则完全不同，当页面处理未激活的状态下，该页面的屏幕刷新任务也会被系统暂停，因此跟着系统走的RequestAnimationFrame也会停止渲染，当页面被激活时，动画就从上次停留的地方继续执行，有效节省了CPU开销。
- **函数节流**：在高频率事件( resize, scroll 等)中，为了防止在一个刷新间隔内发生多次函数执行，RequestAnimationFrame可保证每个刷新间隔内，函数只被执行一次，这样既能保证流畅性，也能更好的节省函数执行的开销，一个刷新间隔内函数执行多次时没有意义的，因为多数显示器每16.7ms刷新一次，多次绘制并不会在屏幕上体现出来。
- **减少DOM操作**：requestAnimationFrame 会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率，一般来说，这个频率为每秒60帧。

**setTimeout执行动画的缺点**：它通过设定间隔时间来不断改变图像位置，达到动画效果。但是容易出现卡顿、抖动的现象；原因是：

- settimeout任务被放入异步队列，只有当主线程任务执行完后才会执行队列中的任务，因此实际执行时间总是比设定时间要晚；
- settimeout的固定时间间隔不一定与屏幕刷新间隔时间相同，会引起丢帧。




### 12.精灵图的应用和常见图片格式

CSSSprites（精灵图），将一个页面涉及到的所有图片都包含到一张大图中去，然后利用CSS的 background-image，background-repeat，background-position属性的组合进行背景定位。

**优点：**

- 利用`CSS Sprites`能很好地减少网页的http请求，从而大大提高了页面的性能，这是`CSS Sprites`最大的优点；
- `CSS Sprites`能减少图片的字节，把3张图片合并成1张图片的字节总是小于这3张图片的字节总和。

**缺点：**

- 在图片合并时，要把多张图片有序的、合理的合并成一张图片，还要留好足够的空间，防止板块内出现不必要的背景。在宽屏及高分辨率下的自适应页面，如果背景不够宽，很容易出现背景断裂；
- `CSSSprites`在开发的时候相对来说有点麻烦，需要借助`photoshop`或其他工具来对每个背景单元测量其准确的位置。
- 维护方面：`CSS Sprites`在维护的时候比较麻烦，页面背景有少许改动时，就要改这张合并的图片，无需改的地方尽量不要动，这样避免改动更多的`CSS`，如果在原来的地方放不下，又只能（最好）往下加图片，这样图片的字节就增加了，还要改动`CSS`。

#### 常见图片格式

（1）**BMP**，是无损的、既支持索引色也支持直接色的点阵图。这种图片格式几乎没有对数据进行压缩，所以BMP格式的图片通常是较大的文件。

（2）**GIF**是无损的、采用索引色的点阵图。采用LZW压缩算法进行编码。文件小，是GIF格式的优点，同时，GIF格式还具有支持动画以及透明的优点。但是GIF格式仅支持8bit的索引色，所以GIF格式适用于对色彩要求不高同时需要文件体积较小的场景。

（3）**JPEG**是有损的、采用直接色的点阵图。JPEG的图片的优点是采用了直接色，得益于更丰富的色彩，JPEG非常适合用来存储照片，与GIF相比，JPEG不适合用来存储企业Logo、线框类的图。因为有损压缩会导致图片模糊，而直接色的选用，又会导致图片文件较GIF更大。

（4）**PNG-8**是无损的、使用索引色的点阵图。PNG是一种比较新的图片格式，PNG-8是非常好的GIF格式替代者，在可能的情况下，应该尽可能的使用PNG-8而不是GIF，因为在相同的图片效果下，PNG-8具有更小的文件体积。除此之外，PNG-8还支持透明度的调节，而GIF并不支持。除非需要动画的支持，否则没有理由使用GIF而不是PNG-8。

（5）**PNG-24**是无损的、使用直接色的点阵图。PNG-24的优点在于它压缩了图片的数据，使得同样效果的图片，PNG-24格式的文件大小要比BMP小得多。当然，PNG24的图片还是要比JPEG、GIF、PNG-8大得多。

（6）**SVG**是无损的矢量图。SVG是矢量图意味着SVG图片由直线和曲线以及绘制它们的方法组成。当放大SVG图片时，看到的还是线和曲线，而不会出现像素点。SVG图片在放大时，不会失真，所以它适合用来绘制Logo、Icon等。

（7）**WebP**是谷歌开发的一种新图片格式，WebP是同时支持有损和无损压缩的、使用直接色的点阵图。从名字就可以看出来它是为Web而生的，什么叫为Web而生呢？就是说相同质量的图片，WebP具有更小的文件体积。现在网站上充满了大量的图片，如果能够降低每一个图片的文件大小，那么将大大减少浏览器和服务器之间的数据传输量，进而降低访问延迟，提升访问体验。目前只有Chrome浏览器和Opera浏览器支持WebP格式，兼容性不太好。

- 在无损压缩的情况下，相同质量的WebP图片，文件大小要比PNG小26%；
- 在有损压缩的情况下，具有相同图片精度的WebP图片，文件大小要比JPEG小25%~34%；
- WebP图片格式支持图片透明度，一个无损压缩的WebP图片，如果要支持透明度只需要22%的格外文件大小。

### 13.什么是css预处理器，如何看待css工程化

####  CSS预处理器/后处理器

**预处理器，** 如：`less`，`sass`，`stylus`，用来预编译`sass`或者`less`，增加了`css`代码的复用性。层级，`mixin`， 变量，循环， 函数等对编写以及开发UI组件都极为方便。

**后处理器，** 如： `postCss`，通常是在完成的样式表中根据`css`规范处理`css`，让其更加有效。目前最常做的是给`css`属性添加浏览器私有前缀，实现跨浏览器兼容性的问题。

`css`预处理器为`css`增加一些编程特性，无需考虑浏览器的兼容问题，可以在`CSS`中使用变量，简单的逻辑程序，函数等在编程语言中的一些基本的性能，可以让`css`更加的简洁，增加适应性以及可读性，可维护性等。

其它`css`预处理器语言：`Sass（Scss）`, `Less`, `Stylus`, `Turbine`, `Swithch css`, `CSS Cacheer`, `DT Css`。

使用原因：

- 结构清晰， 便于扩展
- 可以很方便的屏蔽浏览器私有语法的差异
- 可以轻松实现多重继承
- 完美的兼容了`CSS`代码，可以应用到老项目中

#### css工程化

##### 解决类名冲突

 提供一种命名的标准，来解决冲突

**css in js**       *css in js 的核心思想是：用一个JS对象来描述样式，而不是css样式表*

 **css module  **    启用css module，需要将css-loader的配置modules设置为true css-loader会将样式中的类名进行转换，转换为一个唯一的hash值

#####   重复样式

**css预编译器**   支持变量、函数等高级语法，然后经过编译器将其编译成为正常的css

**postcss**  PostCss和LESS、SASS的思路不同，它其实只做一些代码分析之类的事情，将分析的结果交给插件，具体的代码转换操作是插件去完成的

##### css文件细分问题

**利用webpack拆分css**

`css-loader`干了什么：

1. 将css文件的内容作为字符串导出
2. 将css中的其他依赖作为require导入，以便webpack分析依赖

由于css-loader仅提供了将css转换为字符串导出的能力，剩余的事情要交给其他loader或plugin来处理。style-loader可以将css-loader转换后的代码进一步处理，将css-loader导出的字符串加入到页面的style元素中

### 14. 为什么有时候⽤**translate**来改变位置⽽不是定位？

translate 是 transform 属性的⼀个值。改变transform或opacity不会触发浏览器重新布局（reflow）或重绘（repaint），只会触发复合（compositions）。⽽改变绝对定位会触发重新布局，进⽽触发重绘和复合。transform使浏览器为元素创建⼀个 GPU 图层，但改变绝对定位会使⽤到 CPU。 因此translate()更⾼效，可以缩短平滑动画的绘制时间。 ⽽translate改变位置时，元素依然会占据其原始空间，绝对定位就不会发⽣这种情况。

### 15.什么是媒体查询

使用 `@media`，可以指定一个媒体查询和一个 CSS 块，当且仅当该媒体查询与正在使用其内容的设备匹配时，该 CSS 块才能应用于该文档。

在 CSS3 中，媒体查询可用于检测很多事情，例如：

- `viewport`（视窗）的宽度与高度
- 设备的宽度与高度
- 朝向（智能手机横屏，竖屏）
- 分辨率

使用 @media 查询，你可以针对不同的媒体类型定义不同的样式。

@media 可以针对不同的屏幕尺寸设置不同的样式，特别是如果你需要设置设计响应式的页面，@media 是非常有用的。

当你重置浏览器大小的过程中，页面也会根据浏览器的宽度和高度重新渲染页面。

#### 媒体功能

| 值                      | 描述                                                         |
| :---------------------- | :----------------------------------------------------------- |
| aspect-ratio            | 定义输出设备中的页面可见区域宽度与高度的比率                 |
| color                   | 定义输出设备每一组彩色原件的个数。如果不是彩色设备，则值等于0 |
| color-index             | 定义在输出设备的彩色查询表中的条目数。如果没有使用彩色查询表，则值等于0 |
| device-aspect-ratio     | 定义输出设备的屏幕可见宽度与高度的比率。                     |
| device-height           | 定义输出设备的屏幕可见高度。                                 |
| device-width            | 定义输出设备的屏幕可见宽度。                                 |
| grid                    | 用来查询输出设备是否使用栅格或点阵。                         |
| height                  | 定义输出设备中的页面可见区域高度。                           |
| max-aspect-ratio        | 定义输出设备的屏幕可见宽度与高度的最大比率。                 |
| max-color               | 定义输出设备每一组彩色原件的最大个数。                       |
| max-color-index         | 定义在输出设备的彩色查询表中的最大条目数。                   |
| max-device-aspect-ratio | 定义输出设备的屏幕可见宽度与高度的最大比率。                 |
| max-device-height       | 定义输出设备的屏幕可见的最大高度。                           |
| max-device-width        | 定义输出设备的屏幕最大可见宽度。                             |
| max-height              | 定义输出设备中的页面最大可见区域高度。                       |
| max-monochrome          | 定义在一个单色框架缓冲区中每像素包含的最大单色原件个数。     |
| max-resolution          | 定义设备的最大分辨率。                                       |
| max-width               | 定义输出设备中的页面最大可见区域宽度。                       |
| min-aspect-ratio        | 定义输出设备中的页面可见区域宽度与高度的最小比率。           |
| min-color               | 定义输出设备每一组彩色原件的最小个数。                       |
| min-color-index         | 定义在输出设备的彩色查询表中的最小条目数。                   |
| min-device-aspect-ratio | 定义输出设备的屏幕可见宽度与高度的最小比率。                 |
| min-device-width        | 定义输出设备的屏幕最小可见宽度。                             |
| min-device-height       | 定义输出设备的屏幕的最小可见高度。                           |
| min-height              | 定义输出设备中的页面最小可见区域高度。                       |
| min-monochrome          | 定义在一个单色框架缓冲区中每像素包含的最小单色原件个数       |
| min-resolution          | 定义设备的最小分辨率。                                       |
| min-width               | 定义输出设备中的页面最小可见区域宽度。                       |
| monochrome              | 定义在一个单色框架缓冲区中每像素包含的单色原件个数。如果不是单色设备，则值等于0 |
| orientation             | 定义输出设备中的页面可见区域高度是否大于或等于宽度。         |
| resolution              | 定义设备的分辨率。如：96dpi, 300dpi, 118dpcm                 |
| scan                    | 定义电视类设备的扫描工序。                                   |
| width                   | 定义输出设备中的页面可见区域宽度。                           |

### 16. li 与 li 之间有看不见的空白间隔是什么原因引起的？如何解决？

浏览器会把inline内联元素间的空白字符（空格、换行、Tab等）渲染成一个空格。为了美观，通常是一个`<li>`放在一行，这导致`<li>`换行后产生换行字符，它变成一个空格，占用了一个字符的宽度。

**解决办法：**

（1）为`<li>`设置float:left。不足：有些容器是不能设置浮动，如左右切换的焦点图等。

（2）将所有`<li>`写在同一行。不足：代码不美观。

（3）将`<ul>`内的字符尺寸直接设为0，即font-size:0。不足：`<ul>`中的其他字符尺寸也被设为0，需要额外重新设定其他字符尺寸，且在Safari浏览器依然会出现空白间隔。

（4）消除`<ul>`的字符间隔letter-spacing:-8px，不足：这也设置了`<li>`内的字符间隔，因此需要将`<li>`内的字符间隔设为默认letter-spacing:normal。



### 17.回流和重绘

#### 回流 (Reflow)

当`Render Tree`中部分或全部元素的尺寸、结构、或某些属性发生改变时，浏览器重新渲染部分或全部文档的过程称为回流。

会导致回流的操作：

- 页面首次渲染
- 浏览器窗口大小发生改变
- 元素尺寸或位置发生改变
- 元素内容变化（文字数量或图片大小等等）
- 元素字体大小变化
- 添加或者删除**可见**的`DOM`元素
- 激活`CSS`伪类（例如：`:hover`）
- 查询某些属性或调用某些方法

一些常用且会导致回流的属性和方法：

- `clientWidth`、`clientHeight`、`clientTop`、`clientLeft`
- `offsetWidth`、`offsetHeight`、`offsetTop`、`offsetLeft`
- `scrollWidth`、`scrollHeight`、`scrollTop`、`scrollLeft`
- `scrollIntoView()`、`scrollIntoViewIfNeeded()`
- `getComputedStyle()`
- `getBoundingClientRect()`
- `scrollTo()`

#### 重绘 (Repaint)

当页面中元素样式的改变并不影响它在文档流中的位置时（例如：`color`、`background-color`、`visibility`等），浏览器会将新样式赋予给元素并重新绘制它，这个过程称为重绘。

#### 性能影响

**回流比重绘的代价要更高。**

有时即使仅仅回流一个单一的元素，它的父元素以及任何跟随它的元素也会产生回流。

现代浏览器会对频繁的回流或重绘操作进行优化：

浏览器会维护一个队列，把所有引起回流和重绘的操作放入队列中，如果队列中的任务数量或者时间间隔达到一个阈值的，浏览器就会将队列清空， 你                                                                                              进行一次批处理，这样可以把多次回流和重绘变成一次。

当你访问以下属性或方法时，浏览器会立刻清空队列：

- `clientWidth`、`clientHeight`、`clientTop`、`clientLeft`
- `offsetWidth`、`offsetHeight`、`offsetTop`、`offsetLeft`
- `scrollWidth`、`scrollHeight`、`scrollTop`、`scrollLeft`
- `width`、`height`
- `getComputedStyle()`
- `getBoundingClientRect()`

因为队列中可能会有影响到这些属性或方法返回值的操作，即使你希望获取的信息与队列中操作引发的改变无关，浏览器也会强行清空队列，确保你拿到的值是最精确的。

#### CSS

- 避免使用`table`布局。
- 尽可能在`DOM`树的最末端改变`class`。
- 避免设置多层内联样式。
- 将动画效果应用到`position`属性为`absolute`或`fixed`的元素上。
- 避免使用`CSS`表达式（例如：`calc()`）。

#### JavaScript

- 避免频繁操作样式，最好一次性重写`style`属性，或者将样式列表定义为`class`并一次性更改`class`属性。
- 避免频繁操作`DOM`，创建一个`documentFragment`，在它上面应用所有`DOM操作`，最后再把它添加到文档中。
- 也可以先为元素设置`display: none`，操作结束后再把它显示出来。因为在`display`属性为`none`的元素上进行的`DOM`操作不会引发回流和重绘。
- 避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。
- 对具有复杂动画的元素使用绝对定位，使它脱离文档流，否则会引起父元素及后续元素频繁回流。

### 18.溢出

**CSS overflow 属性控制对太大而区域无法容纳的内容的处理方式。**

overflow 属性指定在元素的内容太大而无法放入指定区域时是剪裁内容还是添加滚动条。

overflow 属性可设置以下值：

-   visible - 默认。溢出没有被剪裁。内容在元素框外渲染
-   hidden - 溢出被剪裁，其余内容将不可见
-   scroll - 溢出被剪裁，同时添加滚动条以查看其余内容
-   auto - 与 scroll 类似，但仅在必要时添加滚动条

**注释：** **overflow** 属性仅适用于具有指定高度的块元素。

#### overflow: visible

默认情况下，溢出是可见的(visible)，这意味着它不会被裁剪，而是在元素框外渲染

#### overflow: hidden

如果使用 hidden 值，溢出会被裁剪，其余内容被隐藏

#### overflow: scroll

如果将值设置为 scroll，溢出将被裁剪，并添加滚动条以便在框内滚动。请注意，这将在水平和垂直方向上添加一个滚动条（即使您不需要它）

#### overflow: auto

auto 值类似于 scroll，但是它仅在必要时添加滚动条

#### overflow-x 和 overflow-y

overflow-x 和 overflow-y 属性规定是仅水平还是垂直地（或同时）更改内容的溢出：

-   overflow-x 指定如何处理内容的左/右边缘。
-   overflow-y 指定如何处理内容的上/下边缘。

### 19.CSS3中有哪些新特性

- 新增各种CSS选择器 （: not(.input)：所有 class 不是“input”的节点）
- 圆角 （border-radius:8px）
- 多列布局 （multi-column layout）
- 阴影和反射 （Shadoweflect）
- 文字特效 （text-shadow）
- 文字渲染 （Text-decoration）
- 线性渐变 （gradient）
- 旋转 （transform）
- 增加了旋转,缩放,定位,倾斜,动画,多背景

### 20. 对**line-height 的理解及其赋值方式**

**（1）line-height的概念：**

- line-height 指一行文本的高度，包含了字间距，实际上是下一行基线到上一行基线距离；
- 如果一个标签没有定义 height 属性，那么其最终表现的高度由 line-height 决定；
- 一个容器没有设置高度，那么撑开容器高度的是 line-height，而不是容器内的文本内容；
- 把 line-height 值设置为 height 一样大小的值可以实现单行文字的垂直居中；
- line-height 和 height 都能撑开一个高度；

**（2）line-height 的赋值方式：**

- 带单位：px 是固定值，而 em 会参考父元素 font-size 值计算自身的行高
- 纯数字：会把比例传递给后代。例如，父级行高为 1.5，子元素字体为 18px，则子元素行高为 1.5 * 18 = 27px
- 百分比：将计算后的值传递给后代

### 20.css模块化

### 21. 对 CSS 工程化的理解

CSS 工程化是为了解决以下问题：

1. **宏观设计**：CSS 代码如何组织、如何拆分、模块结构怎样设计？
2. **编码优化**：怎样写出更好的 CSS？
3. **构建**：如何处理我的 CSS，才能让它的打包结果最优？
4. **可维护性**：代码写完了，如何最小化它后续的变更成本？如何确保任何一个同事都能轻松接手？

以下三个方向都是时下比较流行的、普适性非常好的 CSS 工程化实践：

- 预处理器：Less、 Sass 等；
- 重要的工程化插件： PostCss；
- Webpack loader 等 。

基于这三个方向，可以衍生出一些具有典型意义的子问题，这里我们逐个来看：

**（1）预处理器：为什么要用预处理器？它的出现是为了解决什么问题？**

预处理器，其实就是 CSS 世界的“轮子”。预处理器支持我们写一种类似 CSS、但实际并不是 CSS 的语言，然后把它编译成 CSS 代码： ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3d58c5313e884e38b1545a5896613250~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp) 那为什么写 CSS 代码写得好好的，偏偏要转去写“类 CSS”呢？这就和本来用 JS 也可以实现所有功能，但最后却写 React 的 jsx 或者 Vue 的模板语法一样——为了爽！要想知道有了预处理器有多爽，首先要知道的是传统 CSS 有多不爽。随着前端业务复杂度的提高，前端工程中对 CSS 提出了以下的诉求：

1. 宏观设计上：我们希望能优化 CSS 文件的目录结构，对现有的 CSS 文件实现复用；
2. 编码优化上：我们希望能写出结构清晰、简明易懂的 CSS，需要它具有一目了然的嵌套层级关系，而不是无差别的一铺到底写法；我们希望它具有变量特征、计算能力、循环能力等等更强的可编程性，这样我们可以少写一些无用的代码；
3. 可维护性上：更强的可编程性意味着更优质的代码结构，实现复用意味着更简单的目录结构和更强的拓展能力，这两点如果能做到，自然会带来更强的可维护性。

这三点是传统 CSS 所做不到的，也正是预处理器所解决掉的问题。预处理器普遍会具备这样的特性：

- 嵌套代码的能力，通过嵌套来反映不同 css 属性之间的层级关系 ；
- 支持定义 css 变量；
- 提供计算函数；
- 允许对代码片段进行 extend 和 mixin；
- 支持循环语句的使用；
- 支持将 CSS 文件模块化，实现复用。

**（2）PostCss：PostCss 是如何工作的？我们在什么场景下会使用 PostCss？**

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2911f98bbacf4b1cbffbb9e1527a4977~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp) 它和预处理器的不同就在于，预处理器处理的是 类CSS，而 PostCss 处理的就是 CSS 本身。Babel 可以将高版本的 JS 代码转换为低版本的 JS 代码。PostCss 做的是类似的事情：它可以编译尚未被浏览器广泛支持的先进的 CSS 语法，还可以自动为一些需要额外兼容的语法增加前缀。更强的是，由于 PostCss 有着强大的插件机制，支持各种各样的扩展，极大地强化了 CSS 的能力。

PostCss 在业务中的使用场景非常多：

- 提高 CSS 代码的可读性：PostCss 其实可以做类似预处理器能做的工作；
- 当我们的 CSS 代码需要适配低版本浏览器时，PostCss 的 [Autoprefixer](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fpostcss%2Fautoprefixer) 插件可以帮助我们自动增加浏览器前缀；
- 允许我们编写面向未来的 CSS：PostCss 能够帮助我们编译 CSS next 代码；

**（3）Webpack 能处理 CSS 吗？如何实现？** Webpack 能处理 CSS 吗：

- **Webpack 在裸奔的状态下，是不能处理 CSS 的**，Webpack 本身是一个面向 JavaScript 且只能处理 JavaScript 代码的模块化打包工具；
- Webpack 在 loader 的辅助下，是可以处理 CSS 的。

如何用 Webpack 实现对 CSS 的处理：

- Webpack 中操作 CSS 需要使用的两个关键的 loader：css-loader 和 style-loader
- 注意，答出“用什么”有时候可能还不够，面试官会怀疑你是不是在背答案，所以你还需要了解每个 loader 都做了什么事情：
  - css-loader：导入 CSS 模块，对 CSS 代码进行编译处理；
  - style-loader：创建style标签，把 CSS 内容写入标签。

在实际使用中，**css-loader 的执行顺序一定要安排在 style-loader 的前面**。因为只有完成了编译过程，才可以对 css 代码进行插入；若提前插入了未编译的代码，那么 webpack 是无法理解这坨东西的，它会无情报错。

### 22.css动画性能优化

 [GPU加速原理 动画实现](https://blog.csdn.net/weixin_41017246/article/details/80365121)

提高动画的优化不得不提及浏览器是如何渲染一个页面。在从服务器中拿到数据后，浏览器会先做解析三类东西：

-   解析html,xhtml,svg这三类文档，形成dom树。
-   解析css，产生css rule tree。
-   解析js，js会通过api来操作dom tree和css rule tree。

解析完成之后，浏览器引擎会通过dom tree和css rule tree来构建rendering tree：

-   rendering tree和dom tree并不完全相同，例如：`<head></head>`或display:none的东西就不会放在渲染树中。
-   css rule tree主要是完成匹配，并把css rule附加给rendering tree的每个element。

在渲染树构建完成后，

-   浏览器会对这些元素进行定位和布局，这一步也叫做reflow或者layout。
-   浏览器绘制这些元素的样式，颜色，背景，大小及边框等，这一步也叫做repaint。
-   然后浏览器会将各层的信息发送给GPU，GPU会将各层合成；显示在屏幕上。

reflow => repaint => composite

reflow和repaint都是耗费浏览器性能的操作,为了仅发生composite，我们做动画的css property必须满足以下三个条件：

-   不影响文档流。
-   不依赖文档流。
-   不会造成重绘。

满足以上以上条件的css property只有transform和opacity

这样做有两个优势：

-   动画将会非常流畅
-   动画不在绑定到CPU，即使js执行大量的工作；动画依然流畅

GPU有2个问题：

一个或多个没有自己复合层的元素要出现在有复合层元素的上方，它就会拥有自己的复合层；这种情况被称为隐式合成。

使用GPU动画需要发送多张渲染层的图像给GPU，GPU也需要缓存它们以便于后续动画的使用

-   保持动画的对象的z-index尽可能的高。理想的，这些元素应该是body元素的直接子元素。当然，这不是总可能的。所以你可以克隆一个元素，把它放在body元素下仅仅是为了做动画。
-   将元素上设置will-change CSS属性，元素上有了这个属性，浏览器会提升这个元素成为一个复合层（不是总是）。这样动画就可以平滑的开始和结束。但是不要滥用这个属性，否则会大大增加内存消耗。

css动画有一个重要的特性，它是完全工作在GPU上。因为你声明了一个动画如何开始和如何结束，浏览器会在动画开始前准备好所有需要的指令；并把它们发送给GPU。而如果使用js动画，浏览器必须计算每一帧的状态；为了保证平滑的动画，我们必须在浏览器主线程计算新状态；把它们发送给GPU至少60次每秒。除了计算和发送数据比css动画要慢，主线程的负载也会影响动画； 当主线程的计算任务过多时，会造成动画的延迟、卡顿。

所以尽可能地使用基于css的动画，不仅仅更快；也不会被大量的js计算所阻塞。

浏览器中用css开启硬件加速，使GPU (Graphics Processing Unit) 发挥功能，从而提升性能

**虽然我们可能不想对元素应用3D变换，可我们一样可以开启3D引擎。例如我们可以用transform: translateZ(0); 来开启硬件加速**



这个问题是因为使用transform和opacity做CSS动画的时候，会将元素提升为一个复合层；而使用js操作css属性做动画时，必须使用translateZ或will-change才能将元素强行提升至一个复合层。

元素本身使用transform和opacity做CSS动画的时候，会提前告诉GPU动画如何开始和结束及所需要的指令；所以会创建一个复合层（渲染层），并把页面所有的复合层发送给GPU；作为图像缓存，然后动画的发生仅仅是复合层间相对移动。

而使用js做动画，js必须在动画的每一帧计算元素的状态；发送给GPU，但不会将元素提升至一个复合层；所以想让元素提升至一个复合层，必须使用translateZ或will-change: transform, opacity。

使用 translate3D 会让浏览器开启硬件加速，性能当然就提高了。translateZ变成3d效果，走GPU渲染。这样也有缺点就是耗电和发热问题。同样的canvas也会开启gpu渲染。



### 23. 单行、多行文本溢出隐藏

- 单行文本溢出

```css
overflow: hidden;            // 溢出隐藏
text-overflow: ellipsis;      // 溢出用省略号显示
white-space: nowrap;         // 规定段落中的文本不进行换行
```

- 多行文本溢出

```css
overflow: hidden;            // 溢出隐藏
text-overflow: ellipsis;     // 溢出用省略号显示
display:-webkit-box;         // 作为弹性伸缩盒子模型显示。
-webkit-box-orient:vertical; // 设置伸缩盒子的子元素排列方式：从上到下垂直排列
-webkit-line-clamp:3;        // 显示的行数
```

注意：由于上面的三个属性都是 CSS3 的属性，没有浏览器可以兼容，所以要在前面加一个`-webkit-` 来兼容一部分浏览器。

### 24. 如何判断元素是否到达可视区域

以图片显示为例：

- `window.innerHeight` 是浏览器可视区的高度；
- `document.body.scrollTop || document.documentElement.scrollTop` 是浏览器滚动的过的距离；
- `imgs.offsetTop` 是元素顶部距离文档顶部的高度（包括滚动条的距离）；
- 内容达到显示区域的：`img.offsetTop < window.innerHeight + document.body.scrollTop;`

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c57fc165a4ce4d5b9a2885867d4f1cab~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 25. z-index属性在什么情况下会失效

通常 z-index 的使用是在有两个重叠的标签，在一定的情况下控制其中一个在另一个的上方或者下方出现。z-index值越大就越是在上层。z-index元素的position属性需要是relative，absolute或是fixed。

z-index属性在下列情况下会失效：

- 父元素position为relative时，子元素的z-index失效。解决：父元素position改为absolute或static；
- 元素没有设置position属性为非static属性。解决：设置该元素的position属性为relative，absolute或是fixed中的一种；
- 元素在设置z-index的同时还设置了float浮动。解决：float去除，改为display：inline-block；

## 浮动和定位

### 1.什么是BFC，如何创建

先来看两个相关的概念：

- Box: Box 是 CSS 布局的对象和基本单位，⼀个⻚⾯是由很多个 Box 组成的，这个Box就是我们所说的盒模型。
- Formatting context：块级上下⽂格式化，它是⻚⾯中的⼀块渲染区域，并且有⼀套渲染规则，它决定了其⼦元素将如何定位，以及和其他元素的关系和相互作⽤。

块格式化上下文（Block Formatting Context，BFC）是Web页面的可视化CSS渲染的一部分，是布局过程中生成块级盒子的区域，也是浮动元素与其他元素的交互限定区域。

通俗来讲：BFC是一个独立的布局环境，可以理解为一个容器，在这个容器中按照一定规则进行物品摆放，并且不会影响其它环境中的物品。如果一个元素符合触发BFC的条件，则BFC中的元素布局不受外部影响。

**创建BFC的条件：**

- 根元素：body；
- 元素设置浮动：float 除 none 以外的值；
- 元素设置绝对定位：position (absolute、fixed)；
- display 值为：inline-block、table-cell、table-caption、flex等；
- overflow 值为：hidden、auto、scroll；

**BFC的特点：**

- 垂直方向上，自上而下排列，和文档流的排列方式一致。
- 在BFC中上下相邻的两个容器的margin会重叠
- 计算BFC的高度时，需要计算浮动元素的高度
- BFC区域不会与浮动的容器发生重叠
- BFC是独立的容器，容器内部元素不会影响外部元素
- 每个元素的左margin值和容器的左border相接触

**BFC的作用：**

- **解决margin的重叠问题**：由于BFC是一个独立的区域，内部的元素和外部的元素互不影响，将两个元素变为两个BFC，就解决了margin重叠的问题。
- **解决高度塌陷的问题**：在对子元素设置浮动后，父元素会发生高度塌陷，也就是父元素的高度变为0。解决这个问题，只需要把父元素变成一个BFC。常用的办法是给父元素设置`overflow:hidden`。
- **创建自适应两栏布局**：可以用来创建自适应两栏布局：左边的宽度固定，右边的宽度自适应。

```css
.left{
     width: 100px;
     height: 200px;
     background: red;
     float: left;
 }
 .right{
     height: 300px;
     background: blue;
     overflow: hidden;
 }
 
<div class="left"></div>
<div class="right"></div>
```

左侧设置`float:left`，右侧设置`overflow: hidden`。这样右边就触发了BFC，BFC的区域不会与浮动元素发生重叠，所以两侧就不会发生重叠，实现了自适应两栏布局。

### 2.什么是高度塌陷

当父元素不设置高度的时候靠子元素撑大，也就是说子元素有多高，父元素就有多高，当子元素浮动后，父元素就会高度塌陷。父元素高度塌陷后，父元素下面的元素就会向上移动，这样会导致整个页面的布局混乱。

**比如下面的效果：**

```css
<div class="box1">
	<div class="box2"></div>
</div>
	<div class="box3"></div>
.box1 {
		border: 10px red solid;
	    }
.box2 {
		width: 100px;
		height: 100px;
		background-color: blue;
		float: left;
	    }
.box3 {
		height: 100px;
		background-color: green;
		}
```



![image-20220406152622355](https://s2.loli.net/2022/04/06/CqyAlm91iepgPHD.png)

**可以看到父元素高度塌陷后，下面的子元素都往上移动了，脱离了文档流。**

#### 方法一:

**给父元素设置固定的高度，固定高度后，父元素的高度就无法自适应子元素的高度了。 弊端：但是不能让元素高度自适应了 。**

```css
.box1 {
		border: 10px red solid;
		height:100px;
		}
```



![image-20220406152647215](https://s2.loli.net/2022/04/06/ZgcUwvOL5luB9VM.png)



#### 方法二：

**给父元素设置overflow：hidden，解决高度塌陷并能实现高度自适应的方法（遵循BFC的显示原则） 弊端：只要里面的内容或者元素超出父元素以外，就会被隐藏；**

```css
.box1 {
		border: 10px red solid;
		overflow: hidden;
		}
```

#### 方法三：

**在浮动元素的下方添加一个空元素，并且给他设置一下属性。 弊端：会添加很多空标记，增加结构负担，产生代码冗余；**

```css
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<style type="text/css">
			.box1 {
				border: 10px red solid;
			}
			
			.box2 {
				width: 100px;
				height: 100px;
				background-color: blue;
				float: left;
			}
			
			.clear_fix{   /*空白div的属性*/
				clear:both; /*清除两侧浮动*/
				height:0;
				overflow:hidden;
			}
		</style>
	</head>
	<body>
		<div class="box1">
			<div class="box2"></div>
			<div class="clear_fix"></div> /*添加空白div*/
		</div>
	</body>

```

#### 方法四：

**display:table; 给父元素添加display：table；让父元素转换元素类型跟表格的特性一样； 弊端：会改变当前元素的元素类型；**

```css
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<style type="text/css">
			.box1 {
				border: 10px red solid;
				display: table; /* 改变元素类型为表格 */
			}
			
			.box2 {
				width: 100px;
				height: 100px;
				background-color: blue;
				float: left;
			}
		</style>
	</head>
	<body>
		<div class="box1">
			<div class="box2"></div>
		</div>
	</body>
```



![image-20220406152709166](https://s2.loli.net/2022/04/06/9TBdGSQPpOu4LYk.png)



#### 方法五：万能清除法

通过after伪类元素添加一个空白的块元素，css加下列属性，**并给要清除的div加上clear_fix的类名**。 **推荐使用的方式，没有什么副作用。**

```css
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<style type="text/css">
			.box1 {
				border:   red solid;
			}

			.box2 {
				width: 100px;
				height: 100px;
				background-color: blue;
				float: left;
			}

			.clear_fix:after { /* 要加的属性 */
				content: ""; /* 添加内容 */
				clear: both; /* 清楚两侧浮动 */
				display: block; /* 转换元素类型为块元素 */
				height: 0;
				overflow: hidden; /* 溢出隐藏属性 */
				visibility: hidden;/* 隐藏属性 */
			}
		</style>
	</head>
	<body>
		<div class="box1 clear_fix">
			<div class="box2"></div>
		</div>
	</body>
```



![image-20220406152722989](https://s2.loli.net/2022/04/06/nl6YJEzBu9Roibr.png)

## 四、如何清理浮动？

**清除浮动不是不用浮动，清除浮动产生的父容器高度塌陷**。

### 套路1：给浮动元素的父元素添加高度（扩展性不好）

如果一个元素要浮动，那么它的父元素一定要有高度。高度的盒子，才能关住浮动。可以通过直接给父元素设置height，实际应用中我们不大可能给所有的盒子加高度，不仅麻烦，并且不能适应页面的快速变化；另外一种，父容器的高度可以通过内容撑开（比如img图片），实际当中此方法用的比较多。

### 套路2：clear:both;

在最后一个子元素新添加最后一个冗余元素，然后将其设置clear:both,这样就可以清除浮动。这里强调一点，即**在父级元素末尾添加的元素必须是一个块级元素，否则无法撑起父级元素高度**。

```ini
	<div id="wrap">
	    <div id="inner"></div>
	    <div style="clear: both;"></div>
	</div>
复制代码
	#wrap{
	      border: 1px solid;
	}
	#inner{
	      float: left;
	      width: 200px;
	      height: 200px;
	      background: pink;
	}
复制代码
```

### 套路3：伪元素清除浮动

上面那种办法固然可以清除浮动，但是我们不想在页面中添加这些没有意义的冗余元素，此时如何清除浮动吗？ **结合 :after 伪元素和 IEhack ，可以完美兼容当前主流的各大浏览器，这里的 IEhack 指的是触发 hasLayout**。

```bash
<div id="wrap" class="clearfix">
    <div id="inner"></div>
</div>
复制代码
      #wrap {
        border: 1px solid;
      }
      #inner {
        float: left;
        width: 200px;
        height: 200px;
        background: pink;
      }
      /*开启haslayout*/
      .clearfix {
        *zoom: 1;
      }
      /*ie6 7 不支持伪元素*/
      .clearfix:after {
        content: '';
        display: block;
        clear: both;
        height:0;
        line-height:0;
        visibility:hidden;//允许浏览器渲染它，但是不显示出来
      }
复制代码
```

给浮动元素的父容器添加一个clearfix的class，然后给这个class添加一个:after伪元素，实现元素末尾添加一个看不见的块元素来清理浮动。这是通用的清理浮动方案，推荐使用

### 套路4：给父元素使用overflow:hidden;

这种方案让父容器形成了BFC（块级格式上下文），而BFC可以包含浮动，通常用来解决浮动父元素高度坍塌的问题。

**BFC的触发方式**

我们可以给父元素添加以下属性来触发BFC：

- float 为 left | right
- overflow 为 hidden | auto | scorll
- display 为 table-cell | table-caption | inline-block
- position 为 absolute | fixed

这里可以给父元素设置overflow:auto，但是为了兼容IE最好使用overflow:hidden。

**但这种办法有个缺陷：如果有内容出了盒子，用这种方法就会把多的部分裁切掉，所以这时候不能使用。**

**BFC的主要特征:**

- BFC容器是一个隔离的容器，和其他元素互不干扰；所以我们可以用触发两个元素的BFC来解决垂直边距折叠问题。
- BFC不会重叠浮动元素
- BFC可以包含浮动,这可以清除浮动。

### 套路5：br标签清浮动

**br标签存在一个属性：clear。这个属性就是能够清除浮动的利器，在br标签中设置属性clear，并赋值all。即能清除掉浮动**。

```ini
    <div id="wrap">
      <div id="inner"></div>
      <br clear="all" />
    </div>
复制代码
      #wrap {
        border: 1px solid;
      }
      #inner {
        float: left;
        width: 200px;
        height: 200px;
        background: pink;
      }
```

## 文档流

##### 一 normal flow

1.  什么是文档流？文档中元素的默认流动方向，是CSS世界中的一种基本的定位和布局机制。

##### 二 流动方向

1.  inline元素从左到右，到达最右边才会换行。
2.  block元素从上到下，每一个都另起一行。
3.  inline-block也是从左到右，但不会从中间断开。

![image-20220406152744874](https://s2.loli.net/2022/04/06/vYhiec6jTK97BLO.png)

##### 三 三种元素的宽度

1.  inline元素，宽度为内部inline元素的和，不能用width指定。
2.  block元素，默认width:auto，自动计算宽度，可用width指定。
3.  inline-block元素，结合前两者特点，默认宽度为内部inline元素的和，同时可以用width指定宽度。

##### 四 三种元素的高度

1.  inline元素，高度由line-height间接确定，跟height无关。
2.  block元素，高度由内部文档流元素决定，可以设height。
3.  inline-block元素，跟block类似，可以设height。

##### 五 特殊情况，overflow溢出

1.  当内容的宽度或高度大于容易时，会溢出。
2.  overflow可以设置overflow-x和overflow-y
3.  auto表示灵活设置，当内容溢出时显示滚动条，未溢出则不显示
4.  scroll永远显示滚动条
5.  hidden隐藏溢出内容
6.  visible显示溢出部分。

##### 六 脱离文档流

1.  哪些元素会脱离文档流？如果一个元素有float属性或者position:absolute/fixed，就会脱离文档流

**浮动的定义：** 非IE浏览器下，容器不设高度且子元素浮动时，容器高度不能被内容撑开。 此时，内容会溢出到容器外面而影响布局。这种现象被称为浮动（溢出）。

**浮动的工作原理：**

- 浮动元素脱离文档流，不占据空间（引起“高度塌陷”现象）
- 浮动元素碰到包含它的边框或者其他浮动元素的边框停留

浮动元素可以左右移动，直到遇到另一个浮动元素或者遇到它外边缘的包含框。浮动框不属于文档流中的普通流，当元素浮动之后，不会影响块级元素的布局，只会影响内联元素布局。此时文档流中的普通流就会表现得该浮动框不存在一样的布局模式。当包含框的高度小于浮动框的时候，此时就会出现“高度塌陷”。

**浮动元素引起的问题？**

- 父元素的高度无法被撑开，影响与父元素同级的元素
- 与浮动元素同级的非浮动元素会跟随其后
- 若浮动的元素不是第一个元素，则该元素之前的元素也要浮动，否则会影响页面的显示结构

clear 属性指定哪些元素可以浮动于被清除元素的旁边以及哪一侧。

clear 属性可设置以下值之一：

-   none - 允许两侧都有浮动元素。默认值
-   left - 左侧不允许浮动元素
-   right- 右侧不允许浮动元素
-   both - 左侧或右侧均不允许浮动元素
-   inherit - 元素继承其父级的 clear 值

使用 clear 属性的最常见用法是在元素上使用了 float 属性之后。

在清除浮动时，应该对清除与浮动进行匹配：如果某个元素浮动到左侧，则应清除左侧。您的浮动元素会继续浮动，但是被清除的元素将显示在其下方。

**清除浮动的方式如下：**

- 给父级div定义`height`属性
- 最后一个浮动元素之后添加一个空的div标签，并添加`clear:both`样式
- 包含浮动元素的父级标签添加`overflow:hidden`或者`overflow:auto`
- 添加一个`clearfix`类  使用 **::after** 伪元素。由于IE6-7不支持 :after，使用 zoom:1 触发 hasLayout

```css
.clearfix::after{
    content: "\200B";
    display: block; 
    height: 0;
    clear: both;
  }
  .clearfix{
    *zoom: 1;
  }
```

![image-20220508102046805](https://s2.loli.net/2022/05/08/FaY728zCZLnBTOr.png)



### 4.position有哪些属性，各有什么区别

position有以下属性值：

|  属性值  | 概述                                                         |
| :------: | ------------------------------------------------------------ |
| absolute | 生成绝对定位的元素，相对于非static定位的一个父元素进行定位。元素的位置通过left、top、right、bottom属性进行规定。没有就以html元素为基准 脱离文档流 |
| relative | 生成相对定位的元素，相对于其原来的位置进行定位。元素的位置通过left、top、right、bottom属性进行规定。 |
|  fixed   | 生成绝对定位的元素，指定元素相对于屏幕视⼝（viewport）的位置来指定元素位置。元素的位置在屏幕滚动时不会改变，⽐如回到顶部的按钮⼀般都是⽤此定位⽅式。相对于浏览器窗口进行定位，怎么拖动滚动条都不会发生变化    脱离文档流 |
|  static  | 默认值，没有定位，元素出现在正常的文档流中，会忽略 top, bottom, left, right 或者 z-index 声明，块级元素从上往下纵向排布，⾏级元素从左向右排列。 |
| inherit  | 规定从父元素继承position属性的值                             |

前面三者的定位方式如下：

- **relative：** 元素的定位永远是相对于元素自身位置的，和其他元素没关系，也不会影响其他元素。

![image-20220406152810964](https://s2.loli.net/2022/04/06/kJMrWaPsqRpdcjI.png)

- **fixed：** 元素的定位是相对于 window （或者 iframe）边界的，和其他元素没有关系。

![image-20220406152828799](https://s2.loli.net/2022/04/06/5SrcK68ilnOyVDT.png)

- **absolute：** 元素的定位相对于前两者要复杂许多。如果为 absolute 设置了 top、left，浏览器会根据什么去确定它的纵向和横向的偏移量呢？答案是浏览器会递归查找该元素的所有父元素，如果找到一个设置了`position:relative/absolute/fixed`的元素，就以该元素为基准定位，如果没找到，就以浏览器边界定位。如下两个图所示：

![image-20220406152849835](https://s2.loli.net/2022/04/06/Rfj8yn1v4ewa9gF.png) ![image-20220406152905966](https://s2.loli.net/2022/04/06/O7vBwtVfGAUEY3z.png)

>   在绝大多数情况下，子元素的绝对定位都是相对于父元素进行定位的，虽然给父元素的position设置relative、absolute、fixed都可以，但是如果不希望父元素脱标，常用解决方案是：

-   父元素设置`position: relative;`（让父元素成为定位元素，又不脱离标准流）；
-   子元素设置`position: absolute;`；

对于绝对定位的元素，会满足以下两个公式：

-   定位参照对象的宽度 = left + right + margin-left + margin-right + 绝对定位元素实际占用宽度；
-   定位参照对象的高度 = top + bottom + margin-top + margin-bottom + 绝对定位元素实际占用高度；

![image-20220406152928661](https://s2.loli.net/2022/04/06/6TNdFaqjZo4HYJs.png)

如果希望绝对定位元素的宽高和定位参照对象一样，可以给绝对定位元素设置以下属性，这也是为什么定位能够实现盒子的水平垂直居中。

-   left、right、top、bottom全部设置为0，margin设置为auto；
-     根据上面公式，margin + 绝对定位元素实际占用宽高 = 定位参照对象的宽高，盒子自然就水平垂直居中显示了

**position: sticky**

元素根据用户的滚动位置进行定位。

粘性元素根据滚动位置在相对（relative）和固定（fixed）之间切换。起先它会被相对定位，直到在视口中遇到给定的偏移位置为止 - 然后将其“粘贴”在适当的位置（比如 position:fixed）

### 5.什么是外边距重叠

**问题描述：** 两个块级元素的上外边距和下外边距可能会合并（折叠）为一个外边距，其大小会取其中外边距值大的那个，这种行为就是外边距折叠。需要注意的是，**浮动的元素和绝对定位**这种脱离文档流的元素的外边距不会折叠。重叠只会出现在**垂直方向**。

**计算原则：** 折叠合并后外边距的计算原则如下：

- 如果两者都是正数，那么就去最大者
- 如果是一正一负，就会正值减去负值的绝对值
- 两个都是负值时，用0减去两个中绝对值大的那个

**解决办法：** 对于折叠的情况，主要有两种：**兄弟之间重叠**和**父子之间重叠** 

（1）兄弟之间重叠

- 底部元素变为行内盒子：`display: inline-block`
- 底部元素设置浮动：`float`
- 底部元素的position的值为`absolute/fixed`

（2）父子之间重叠

- 父元素加入：`overflow: hidden`
- 父元素添加透明边框：`border:1px solid transparent`
- 子元素变为行内盒子：`display: inline-block`
- 子元素加入浮动属性或定位

------

## 页面布局

### 1.两栏布局实现

一般两栏布局指的是**左边一栏宽度固定，右边一栏宽度自适应**，两栏布局的具体实现：

- 利用浮动，将左边元素宽度设置为200px，并且设置向左浮动。将右边元素的margin-left设置为200px，宽度设置为auto（默认为auto，撑满整个父元素）。

```css
.outer {
  height: 100px;
}
.left {
  float: left;
  width: 200px;
  background: tomato;
}
.right {
  margin-left: 200px;
  width: auto;
  background: gold;
}
```

- 利用浮动，左侧元素设置固定大小，并左浮动，右侧元素设置overflow: hidden; 这样右边就触发了BFC，BFC的区域不会与浮动元素发生重叠，所以两侧就不会发生重叠。

```css
.left{
     width: 100px;
     height: 200px;
     background: red;
     float: left;
 }
 .right{
     height: 300px;
     background: blue;
     overflow: hidden;
 }
```

- 利用flex布局，将左边元素设置为固定宽度200px，将右边的元素设置为flex:1。

```css
.outer {
  display: flex;
  height: 100px;
}
.left {
  width: 200px;
  background: tomato;
}
.right {
  flex: 1;
  background: gold;
}
```

- 利用绝对定位，将父级元素设置为相对定位。左边元素设置为absolute定位，并且宽度设置为200px。将右边元素的margin-left的值设置为200px。

```css
.outer {
  position: relative;
  height: 100px;
}
.left {
  position: absolute;
  width: 200px;
  height: 100px;
  background: tomato;
}
.right {
  margin-left: 200px;
  background: gold;
}
```

- 利用绝对定位，将父级元素设置为相对定位。左边元素宽度设置为200px，右边元素设置为绝对定位，左边定位为200px，其余方向定位为0。

```css
.outer {
  position: relative;
  height: 100px;
}
.left {
  width: 200px;
  background: tomato;
}
.right {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 200px;
  background: gold;
}
```

### 2.三栏布局实现

三栏布局一般指的是页面中一共有三栏，**左右两栏宽度固定，中间自适应的布局**，三栏布局的具体实现：

- 利用**绝对定位**，左右两栏设置为绝对定位，中间设置对应方向大小的margin的值。

```css
.outer {
  position: relative;
  height: 100px;
}

.left {
  position: absolute;
  width: 100px;
  height: 100px;
  background: tomato;
}

.right {
  position: absolute;
  top: 0;
  right: 0;
  width: 200px;
  height: 100px;
  background: gold;
}

.center {
  margin-left: 100px;
  margin-right: 200px;
  height: 100px;
  background: lightgreen;
}
```

- 利用flex布局，左右两栏设置固定大小，中间一栏设置为flex:1。

```css
.outer {
  display: flex;
  height: 100px;
}

.left {
  width: 100px;
  background: tomato;
}

.right {
  width: 100px;
  background: gold;
}

.center {
  flex: 1;
  background: lightgreen;
}
```

- 利用浮动，左右两栏设置固定大小，并设置对应方向的浮动。中间一栏设置左右两个方向的margin值，注意这种方式**，中间一栏必须放到最后：**

```css
.outer {
  height: 100px;
}

.left {
  float: left;
  width: 100px;
  height: 100px;
  background: tomato;
}

.right {
  float: right;
  width: 200px;
  height: 100px;
  background: gold;
}

.center {
  height: 100px;
  margin-left: 100px;
  margin-right: 200px;
  background: lightgreen;
}
```

- 圣杯布局，利用浮动和负边距来实现。父级元素设置左右的 padding，三列均设置向左浮动，中间一列放在最前面，宽度设置为父级元素的宽度，因此后面两列都被挤到了下一行，通过设置 margin 负值将其移动到上一行，再利用相对定位，定位到两边。
- 中间元素设置padding  先浮动  再负外边距  再相对定位移动

```css
.outer {
  height: 100px;
  padding-left: 100px;
  padding-right: 200px;
}

.left {
  position: relative;
  left: -100px;

  float: left;
  margin-left: -100%;

  width: 100px;
  height: 100px;
  background: tomato;
}

.right {
  position: relative;
  left: 200px;

  float: right;
  margin-left: -200px;

  width: 200px;
  height: 100px;
  background: gold;
}

.center {
  float: left;

  width: 100%;
  height: 100px;
  background: lightgreen;
}
```

- 双飞翼布局，双飞翼布局相对于圣杯布局来说，左右位置的保留是通过中间列的 margin 值来实现的，而不是通过父元素的 padding 来实现的。本质上来说，也是通过浮动和外边距负值来实现的。
- wrapper包center  中间元素设左右margin  先浮动  再负外边距  

```css
.outer {
  height: 100px;
}

.left {
  float: left;
  margin-left: -100%;

  width: 100px;
  height: 100px;
  background: tomato;
}

.right {
  float: left;
  margin-left: -200px;

  width: 200px;
  height: 100px;
  background: gold;
}

.wrapper {
  float: left;

  width: 100%;
  height: 100px;
  background: lightgreen;
}

.center {
  margin-left: 100px;
  margin-right: 200px;
  height: 100px;
}
```

### 3.实现水平垂直居中

- 利用绝对定位，先将元素的左上角通过top:50%和left:50%定位到页面的中心，然后再通过translate来调整元素的中心点到页面的中心。该方法需要**考虑浏览器兼容问题。**

```css
.parent {   
    position: relative;
} 
.child {    
    position: absolute; 
    left: 50%; 
    top: 50%;    
    transform: translate(-50%,-50%);
}
```

- 利用绝对定位，设置四个方向的值都为0，并将margin设置为auto，由于宽高固定，因此对应方向实现平分，可以实现水平和垂直方向上的居中。该方法适用于**盒子有宽高**的情况：

```css
.parent {
    position: relative;
}
 
.child {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
}
```

- 利用绝对定位，先将元素的左上角通过top:50%和left:50%定位到页面的中心，然后再通过margin负值来调整元素的中心点到页面的中心。该方法适用于**盒子宽高已知**的情况

```css
.parent {
    position: relative;
}
 
.child {
    position: absolute;
    top: 50%;
    left: 50%;
    margin-top: -50px;     /* 自身 height 的一半 */
    margin-left: -50px;    /* 自身 width 的一半 */
}
```

- 使用flex布局，通过align-items:center和justify-content:center设置容器的垂直和水平方向上为居中对齐，然后它的子元素也可以实现垂直和水平的居中。该方法要**考虑兼容的问题**，该方法在移动端用的较多：

```css
.parent {
    display: flex;
    justify-content:center;
    align-items:center;
}
```

### 4.flex布局怎么做 有哪些应用场景  flex:1是什么意思

#### 一、flex弹性的概念：

弹性盒子是一种用于按行或按列布局元素的一维布局方法，元素可以膨胀以填充额外的空间，收缩以适应更小的空间，适用于任何元素上，如果一个元素使用了flex弹性布局（以下都会简称为：flex布局），则会在内部形成[BFC](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FGuide%2FCSS%2FBlock_formatting_context)，flex布局已经得到了所有浏览器的支持，这意味着，现在就能放心，安全的使用这项技术。

![image-20220406152950382](https://s2.loli.net/2022/04/06/CPmyExc9Yj3BwuG.png)

#### 二、主轴与交叉轴：

学习flex布局需要明白”主轴“与”交叉轴“的概念，采用flex布局的元素，称为”容器“ （ flex container），它的所有子元素都是容器的”项目“（flex item），容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做 `main start` ，结束位置叫做 `main end` ；交叉轴的开始位置叫做 `cross start` ，结束位置叫做 `cross end` 。

![image-20220406153012772](https://s2.loli.net/2022/04/06/a9Sq2vI6x7kgPGM.png)

#### 三、容器的属性

容器和项目各有6个属性，见下图

![image-20220406153243934](https://s2.loli.net/2022/04/06/2yatDN43HlBZQjw.png)

![image-20220406153101393](https://s2.loli.net/2022/04/06/no13dj72HLtZSfI.png)

#####  3.1 flex-direction

`flex-direction` 属性决定主轴的方向（即项目的排列方向）

```css
flex-direction: row | row-reverse | column | column-reverse;
```

- `row`（默认值）：主轴为水平方向，起点在左端。
- `row-reverse`：主轴为水平方向，起点在右端。
- `column`：主轴为垂直方向，起点在上沿。
- `column-reverse`：主轴为垂直方向，起点在下沿。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0445e883e66742aea92aa42f675dd9d8~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 3.2 flex-wrap

默认情况下，项目都排在一条线上，无论是否给定宽度，都是不会主动换行的：

```css
.main {
    width: 500px;
    height: 300px;
    background: skyblue;
    display: flex;
}

.main div {
    width: 100px;
    height: 100px;
    background: pink;
    font-size: 20px;
}
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/beb1756393394691bedc34f5089352a3~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

如果需要换行，需要设置flex-wrap

```css
  flex-wrap: nowrap | wrap | wrap-reverse;
```

- `nowrap`（默认值）：不换行。
- `wrap`：换行，第一行在上方。
- `wrap-reverse`：换行，第一行在下方。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b7347d79aed24c40b47f4487b4fb2002~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 3.3 flex-flow

`flex-flow` 属性是 `flex-direction` 属性和 `flex-wrap` 属性的简写形式，默认值为 `row nowrap`
`flex-flow`: `<flex-direction>`|| `<flex-wrap>`;

##### 3.4 justify-content

`justify-content` 属性定义了项目在**主轴上的对齐方式**    主轴上整个列和列项之间距离关系

```css
justify-content: flex-start | flex-end | center | space-around | space-between | space-between;
```

- `flex-start`（默认值）：左对齐
- `flex-end`：右对齐
- `center`： 居中
- `space-around`：每个项目两侧的间隔相等。
- `space-between`：两端对齐，项目之间的间隔都相等。
- `space-evenly`：每个项目的间隔与项目和容器之间的间隔是相等的。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bbc1f587b35248e8ac9e22c2f65e74e1~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 3.5 align-items

`align-items` 属性定义项目在交叉轴上如何对齐   

```css
align-items: flex-start | flex-end | center | baseline | stretch;
```

- `flex-start`：交叉轴的起点对齐。
- `flex-end`：交叉轴的终点对齐。
- `center`：交叉轴的中点对齐。
- `baseline`: 项目的第一行文字的基线对齐。
- `stretch`（默认值）: 如果项目未设置高度或设为auto，将占满整个容器的高度。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8d13305de6a04c67b080c2c342949a13~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 3.6 align-content

`align-content` 属性定义了多根轴线的对齐方式，前提是需要设置flex-wrap: wrap，否则不会有效

```css
align-content: flex-start | flex-end | center | space-between | space-around | stretch;
```

- `flex-start`：与交叉轴的起点对齐。
- `flex-end`：与交叉轴的终点对齐。
- `center`：与交叉轴的中点对齐。
- `space-between`：与交叉轴两端对齐，轴线之间的间隔平均分布。
- `space-around`：每根轴线两侧的间隔都相等。
- `stretch`（默认值）：轴线占满整个交叉轴。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7aef6b1eaf9242cc99ec31823c8606e6~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### 四、项目的属性

##### 4.1 order

`order` 属性定义项目的排列顺序。数值越小，排列越靠前，默认为0，可以是负数。

```css
order: <integer>;
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7ef20acc88f4921a18ff148a8a6f207~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 4.2 flex-grow

`flex-grow` flex容器中剩余空间的多少应该分配给项目，也称为扩展规则。最终的项目的宽度为：自身宽度 + 容器剩余空间分配宽度，flex-grow最大值是1，超过1按照1来扩展

```css
flex-grow: <number>;
/* default 0 */
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/43b5235e6d174f898e8d7b21949be462~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 4.3 flex-shrink

`flex-shrink` 属性指定了 flex 元素的收缩规则。flex 元素仅在默认宽度之和大于容器的时候才会发生收缩，其收缩的大小是依据 flex-shrink 的值，默认值是1

```css
flex-shrink: <number>;
/* default 1 */
```

默认情况下，第一个div宽度是200，第二个div宽度是300，两个相加应该超过父元素的400，但是由于 flex-shrink 都设置为1，将两个div都收缩在父元素中

```css
.item {  width: 400px;    height: 300px;    background: skyblue;    display: flex;    padding: 5px; }
.item div {    height: 100px;    font-size: 20px;}
.item div:nth-child(1) {    flex-shrink: 1;    width: 200px;    background: pink;}
.item div:nth-child(2) {    flex-shrink: 1;    width: 300px;    background: cadetblue;}
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3c23dd6853934181a0aefbd7c87982ef~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bcbefb1a402a449587595d4cd5aa0cee~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/da115bcb70d94f16b43061db78aa7eb1~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

如果将第一个div设置为 `flex-shrink: 0; `不收缩，则按容器实际宽度展示

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a8da3cba1e2842c0b7eaf30905a3f645~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

那收缩后的子项宽度是怎么样计算的呢？实际上有一个公示：

1. `(200+300)`所有子项的宽度的和 - `(400)`容器的宽度 = `(100)`
2. 第一个子项的宽度占比：`2/5`，第二个子项的宽度占比：`3/5`
3. 则第一个子项的的宽度为：`200` - `2/5 * 100` = 160，第二个子项的宽度为：`300` - `3/5 * 100` = 240

##### 4.4 flex-basis

`flex-basis` 指定了子项在容器主轴方向上的初始大小，优先级高于自身的宽度width

```css
flex-basis: 0 | 100% | auto | <length>
```

宽度是200，但是由于设置了 `flex-basis: 300px; `，所以子项最终宽度是大于自身设置的宽度

```css
.item {  width: 400px;   height: 300px;  background: skyblue;  display: flex;    padding: 5px;}
.item div {  height: 100px;  font-size: 20px;}
.item div {  width: 200px;  flex-basis: 300px;  background: pink;}
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/95a2ff35cf2a49d5b5fe8b2e793436db~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 4.5 flex

`flex` 属性是 `flex-grow` , `flex-shrink` 和 `flex-basis` 的简写，默认值为 `0 1 auto` 。后两个属性可选。

```css
flex: none | [ <'flex-grow'><'flex-shrink'>? || <'flex-basis'>]
```

##### 4.6 align-self

`align-self` 属性允许单个项目有与其他项目不一样的对齐方式，可覆盖 `align-items` 属性。默认值为 `auto` ，表示继承父元素的 `align-items` 属性，如果没有父元素，则等同于 `stretch` 。

```css
align-self: auto | flex-start | flex-end | center | baseline | stretch;

.item {    
    width: 400px;
    height: 300px; 
    background: skyblue;
    display: flex;
    padding: 5px;
}
.item div {    height: 100px;    font-size: 20px;}
.item div {    width: 200px;    flex-basis: 300px;}
.item div:nth-child(1) {    background: pink;    align-self: flex-start;}  
.item div:nth-child(2) {    background: violet;    align-self: center;}  
.item div:nth-child(3) {    background: greenyellow;    align-self: flex-end;}  
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b48825d152e143fa8f633854ac0d2a28~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

flex属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写, 默认值是`0 1 auto`。
`flex-grow`是如果有剩余空间，是否扩大，`0`为不扩大
`flex-shrink`是如果剩余空间不够，是否缩小，`1`为缩小
`flex-basis`为项目本身的大小，默认值是`auto`

#### 简写属性

下面来分别讲讲 `flex:1` `flex:auto`, `flex:0`, `flex:none`的区别

##### flex: 1

`flex: 1`, 对应的是`1 1 0%`, 相当于可扩大，可缩小，`flex-basis`为0%

##### flex: auto

`flex: auto`, 对应的是`1 1 auto`, 相当于可扩大，可缩小，`flex-basis`为`auto`

`flex: 1` 不管内容多少，一般都是**平分空间**，空间大小都一致
而`flex: auto`是根据内容的大小来分，不是均分的（除非内容都是一样，才均分）

##### flex: 0


`flex: 0`, 对应的是`0 1 0%`, 相当于不可扩大，可缩小，`flex-basis`为`0%

##### flex: none

`flex: none`, 对应的是`0 0 auto`, 相当于不可扩大，不可缩小，`flex-basis`为`auto`

`flex: 0` 不可扩大，可缩小,表现形式为最小内容宽度, 
而`flex: none` 不可扩大，不可缩小，内容本身的宽度是多少就是多少

#### 六、布局案例

##### 6.1 等高布局

每一列的内容不一样，但容器的高度时等高的

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/02bb9d56f7b94ce6853f335690e6c20d~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

```css
.item {
width: 400px;    
height: 300px;    
background: skyblue;  
display: flex;   
justify-content: space-between;   
padding: 5px;
}
.item div {    
    width: 100px;   
    font-size: 20px;  
    background: pink; 
}
.item div p {		text-align: center;     }
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/270dfd8a82014b8c8ca6d11aa8fd5e23~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 6.2 左侧宽度固定，右侧自适应布局

常见的TOB系统布局方式，左侧是菜单树，右侧是内容

```css
html, body {
margin: 0;    padding: 0;
}
.container {    display: flex;    width: 100%;    height: 100vh;    background: skyblue; }
.left-tree {    width: 200px;    height: 100%;    background: pink;  }
.main {		flex: 1 1 auto;   }
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ac20d0506b794a3c89eeba7c26434d23~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

##### 6.3 粘性页脚

无论中间的内容有多少，页脚始终在底部展示

```css
html, body {    margin: 0;    padding: 0;}
.container {    display: flex;    flex-direction: column;    width: 100%;    height: 100vh;}
.header {    width: 100%;    height: 60px;    background: pink; }
.main {    flex: 1 1 auto;    background: skyblue;}
.footer {    width: 100%;    height: 60px;    background: pink;   }
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/14416f20ead742049a2c9b36b72016c7~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### 七.简写属性

`flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

```
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

*flex-grow: 1;        flex-shrink: 1;        flex-basis: 0%;*

其中起到最关键作用的就是flex-grow属性了，它指定了flex容器中剩余空间的多少应该分配给项目（flex增长系数），而这个系数如果不设置时默认是0，代表的意义就是即使flex容器有剩余空间，那么项目（flex-item）也不会使用那些剩余空间。所以如果一个flex-item的flex-grow为1而其他的flex-item没有设置，则设置了的就使用剩余空间的1/1*100%=100%的空间。

至于`flex-shrink：1`和`flex-basis: 0%`代表的含义，一个代表flex-item的收缩规则（仅在flex-item宽度总和大于flex-box时才起作用），一个代表flex-item在主轴方向上的初始大小。

### 5.常见布局单位有哪些

常用的布局单位包括像素（`px`），百分比（`%`），`em`，`rem`，`vw/vh`。

**（1）像素**（`px`）是页面布局的基础，一个像素表示终端（电脑、手机、平板等）屏幕所能显示的最小的区域，像素分为两种类型：CSS像素和物理像素：

- **CSS像素**：为web开发者提供，在CSS中使用的一个抽象单位；
- **物理像素**：只与设备的硬件密度有关，任何设备的物理像素都是固定的。

**（2）百分比**（`%`），当浏览器的宽度或者高度发生变化时，通过百分比单位可以使得浏览器中的组件的宽和高随着浏览器的变化而变化，从而实现响应式的效果。一般认为子元素的百分比相对于直接父元素。

**（3）em和rem**相对于px更具灵活性，它们都是相对长度单位，它们之间的区别：**em相对于父元素，rem相对于根元素。**

- **em：** 文本相对长度单位。相对于当前对象内文本的字体尺寸。如果当前行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸(默认16px)。(相对父元素的字体大小倍数)。
- **rem：** rem是CSS3新增的一个相对单位，相对于根元素（html元素）的font-size的倍数。**作用**：利用rem可以实现简单的响应式布局，可以利用html元素中字体的大小与屏幕间的比值来设置font-size的值，以此实现当屏幕分辨率变化时让元素也随之变化。

**（4）vw/vh**是与视图窗口有关的单位，vw表示相对于视图窗口的宽度，vh表示相对于视图窗口高度，除了vw和vh外，还有vmin和vmax两个相关的单位。

- vw：相对于视窗的宽度，视窗宽度是100vw；
- vh：相对于视窗的高度，视窗高度是100vh；
- vmin：vw和vh中的较小值；
- vmax：vw和vh中的较大值；

**vw/vh** 和百分比很类似，两者的区别：

- 百分比（`%`）：大部分相对于祖先元素，也有相对于自身的情况比如（border-radius、translate等)
- vw/vm：相对于视窗的尺寸

#### px、em、rem的区别及使用场景

**三者的区别：**

- px是固定的像素，一旦设置了就无法因为适应页面大小而改变。
- em和rem相对于px更具有灵活性，他们是相对长度单位，其长度不是固定的，更适用于响应式布局。
- em是相对于其父元素来设置字体大小，这样就会存在一个问题，进行任何元素设置，都有可能需要知道他父元素的大小。而rem是相对于根元素，这样就意味着，只需要在根元素确定一个参考值。

**使用场景：**

- 对于只需要适配少部分移动设备，且分辨率对页面影响不大的，使用px即可 。
- 对于需要适配各种移动设备，使用rem，例如需要适配iPhone和iPad等分辨率差别比较挺大的设备。

### 6.怎么进行移动端适配

#### 逻辑分辨率（设备独立像素）

Retina Display(视网膜屏幕)  在iPhone4使用的视网膜屏幕中，把2x2个像素当1个像素使用，这样让屏幕看起来更精致，但是元素的大小却不会改变。从此以后高分辨率的设备，多了一个逻辑像素。这些设备逻辑像素的差别虽然不会跨度很大，但是仍然有点差别，于是便诞生了移动端页面需要适配这个问题，既然逻辑像素由物理像素得来，那他们就会有一个像素比值

#### 设备像素比

设备像素比device pixel ratio简称dpr，即物理像素和设备独立像素的比值。为什么要知道设备像素比呢？因为这个像素比会产生一个非常经典的问题，1像素边框的问题

#### 1px边框问题

> 当我们css里写的1px的时候，由于它是逻辑像素，导致我们的逻辑像素根据这个设备像素比（dpr）去映射到设备上就为2px，或者3px，由于每个设备的屏幕尺寸不一样，就导致每个物理像素渲染出来的大小也不同（记得上面的知识点吗，设备的像素大小是不固定的），这样如果在尺寸比较大的设备上，1px渲染出来的样子相当的粗矿，这就是经典的一像素边框问题

#### 如何解决

核心思路，就是 **在web中，浏览器为我们提供了window.devicePixelRatio来帮助我们获取dpr。在css中，可以使用媒体查询min-device-pixel-ratio，区分dpr：** 我们根据这个像素比，来算出他对应应该有的大小,但是暴露个非常大的兼容问题



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/17/170e773ea67b01b4~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



> 其中Chrome把0.5px四舍五入变成了1px，而firefox/safari能够画出半个像素的边，并且Chrome会把小于0.5px的当成0，而Firefox会把不小于0.55px当成1px，Safari是把不小于0.75px当成1px，进一步在手机上观察iOS的Chrome会画出0.5px的边，而安卓(5.0)原生浏览器是不行的。所以直接设置0.5px不同浏览器的差异比较大，并且我们看到不同系统的不同浏览器对小数点的px有不同的处理。所以如果我们把单位设置成小数的px包括宽高等，其实不太可靠，因为不同浏览器表现不一样。

至于其他解决一像素边框问题网上有一堆答案，在这里我推荐一种非常好用，并且没有副作用的解决方案

**transform: scale(0.5) 方案**

```css
div {
    height:1px;
    background:#000;
    -webkit-transform: scaleY(0.5);
    -webkit-transform-origin:0 0;
    overflow: hidden;
}
```

css根据设备像素比媒体查询后的解决方案

```css
/* 2倍屏 */
@media only screen and (-webkit-min-device-pixel-ratio: 2.0) {
    .border-bottom::after {
        -webkit-transform: scaleY(0.5);
        transform: scaleY(0.5);
    }
}

/* 3倍屏 */
@media only screen and (-webkit-min-device-pixel-ratio: 3.0) {
    .border-bottom::after {
        -webkit-transform: scaleY(0.33);
        transform: scaleY(0.33);
    }
}
```

如此，完美的解决一像素看着粗的问题

#### 如何适配

##### viewport

> 视口(viewport)代表当前可见的计算机图形区域。在Web浏览器术语中，通常与浏览器窗口相同，但不包括浏览器的UI， 菜单栏等——即指你正在浏览的文档的那一部分。

那么在移动端如何配置视口呢？ 简单的一个meta标签即可！

```
<meta name="viewport" content="width=device-width; initial-scale=1; maximum-scale=1; minimum-scale=1; user-scalable=no;">
```

他们分别什么含义呢？



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/17/170e782c3e72b843~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



我们在移动端视口要想视觉效果和体验好，那么我们的视口宽度必去无限接近理想视口

**理想视口：一般来讲，这个视口其实不是真是存在的，它对设备来说是一个最理想布局视口尺寸，在用户不进行手动缩放的情况下，可以将页面理想地展示。那么所谓的理想宽度就是浏览器（屏幕）的宽度了。**

于是上述的meta设置，就是我们的理想设置，他规定了我们的视口宽度为屏幕宽度，初始缩放比例为1，就是初始时候我们的视觉视口就是理想视口！

**其中user-scalable设置为no 可以解决移动端点击事件延迟问题**（拓展）

#### 适配方法

##### 1、rem适配

> rem是CSS3新增的一个相对单位，这个单位引起了广泛关注。这个单位与em有什么区别呢？区别在于使用rem为元素设定字体大小时，仍然是相对大小，但相对的只是HTML根元素。这个单位可谓集相对大小和绝对大小的优点于一身，通过它既可以做到只修改根元素就成比例地调整所有字体大小，又可以避免字体大小逐层复合的连锁反应。目前，除了IE8及更早版本外，所有浏览器均已支持rem。对于不支持它的浏览器，应对方法也很简单，就是多写一个绝对单位的声明。这些浏览器会忽略用rem设定的字体大小

举个例子：

```css
//假设我给根元素的大小设置为14px
html{
    font-size：14px
}
//那么我底下的p标签如果想要也是14像素
p{
    font-size:1rem
}
//如此即可
```

rem的布局 不得不提flexible，flexible方案是阿里早期开源的一个移动端适配解决方案，引用flexible后，我们在页面上统一使用rem来布局。

他的原理非常简单

```
// set 1rem = viewWidth / 10
function setRemUnit () {
    var rem = docEl.clientWidth / 10
    docEl.style.fontSize = rem + 'px'
}
setRemUnit();
```

rem 是相对于html节点的font-size来做计算的。所以在页面初始话的时候给根元素设置一个font-size，接下来的元素就根据rem来布局，这样就可以保证在页面大小变化时，布局可以自适应，

如此我们只需要给设计稿的px转换成对应的rem单位即可

当然，这个方案只是个过渡方案，为什么说是过渡方案

因为当年viewport在低版本安卓设备上还有兼容问题，而vw，vh还没能实现所有浏览器兼容，所以flexible方案用rem来模拟vmin来实现在不同设备等比缩放的“通用”方案，之所以说是通用方案,是因为他这个方案是根据设备大小去判断页面的展示空间大小即屏幕大小，然后根据屏幕大小去百分百还原设计稿，从而让人看到的效果(展示范围)是一样的，这样一来，苹果5 和苹果6p屏幕如果你按照设计稿还原的话，字体大小实际上不一样，而人们在一样的距离上希望看到的大小其实是一样的，本质上，**用户使用更大的屏幕，是想看到更多的内容，而不是更大的字**。

so，这个用缩放来解决问题的方案是个过渡方案，注定时代所淘汰

##### 2、vw，vh布局

> vh、vw方案即将视觉视口宽度 window.innerWidth和视觉视口高度 window.innerHeight 等分为 100 份。



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/17/170e82463b522ff6~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



vh和vw方案和rem类似也是相当麻烦需要做单位转化，而且px转换成vw不一定能完全整除，因此有一定的像素差。

不过在工程化的今天，webpack解析css 的时候用postcss-loader 有个postcss-px-to-viewport能自动实现px到vw的转化

```css

{
    loader: 'postcss-loader',
    options: {
    	plugins: ()=>[
        	require('autoprefixer')({
        		browsers: ['last 5 versions']
        	}),
        	require('postcss-px-to-viewport')({
        		viewportWidth: 375, //视口宽度（数字)
        		viewportHeight: 1334, //视口高度（数字）
        		unitPrecision: 3, //设置的保留小数位数（数字）
        		viewportUnit: 'vw', //设置要转换的单位（字符串）
        		selectorBlackList: ['.ignore', '.hairlines'], //不需要进行转换的类名（数组）
                minPixelValue: 1, //设置要替换的最小像素值（数字）
                mediaQuery: false //允许在媒体查询中转换px（true/false）
        	})
    	]
}

```

##### 3、px为主，vx和vxxx（vw/vh/vmax/vmin）为辅，搭配一些flex（推荐）

之所以推荐使用此种方案，是由于我们要去考虑用户的需求，**用户之所以去买大屏手机，不是为了看到更大的字，而是为了看到更多的内容**，这样直接使用px是最明智的方案

### 7.响应式布局怎么做

#### 1. 媒体查询

`CSS3`媒体查询可以让我们针对不同的媒体类型定义不同的样式，当重置浏览器窗口大小的过程中，页面也会根据浏览器的宽度和高度重新渲染页面。

##### 选择屏幕大小分割点

如何确定媒体查询的分割点也是一个开发中会遇到的问题，根据市场上的移动设备和电脑屏幕分辨率的分布情况，可以发现不同品牌和型号的设备屏幕分辨率一般都不一样

如果我们选择`600px`,`900px`,`1200px`,`1800px`作为分割点，可以适配到常见的14个机型：当然这只是其中的一种分割方案，我们还可以这样划分：`480px`,`800px`,`1400px`,`1400px`

##### 移动优先 OR PC优先

不管是移动优先还是PC优先，都是依据当随着屏幕宽度增大或减小的时候，后面的样式会覆盖前面的样式。因此，移动端优先首先使用的是`min-width`，PC端优先使用的`max-width`。

#### 2.百分比布局

通过百分比单位，可以使得浏览器中组件的宽和高随着浏览器的高度的变化而变化，从而实现响应式的效果。Bootstrap里面的栅格系统就是利用百分比来定义元素的宽高，`CSS3`支持最大最小高，可以将百分比和`max(min)`一起结合使用来定义元素在不同设备下的宽高。

子元素的`height`或`width`中使用百分比，是相对于子元素的直接父元素，`width`相对于父元素的`width`，`height`相对于父元素的`height`；子元素的`top`和`bottom`如果设置百分比，则相对于直接非`static`定位(默认定位)的父元素的高度，同样子元素的`left`和`right`如果设置百分比，则相对于直接非`static`定位(默认定位的)父元素的宽度；子元素的`padding`如果设置百分比，不论是垂直方向或者是水平方向，都相对于直接父亲元素的`width`，而与父元素的`height`无关。跟`padding`一样，`margin`也是如此，子元素的`margin`如果设置成百分比，不论是垂直方向还是水平方向，都相对于直接父元素的`width`；`border-radius`不一样，如果设置`border-radius`为百分比，则是相对于自身的宽度，除了`border-radius`外，还有比如`translate`、`background-size`等都是相对于自身的；

有明显的以下两个缺点：

- 计算困难，如果我们要定义一个元素的宽度和高度，按照设计稿，必须换算成百分比单位。
- 可以看出，各个属性中如果使用百分比，相对父元素的属性并不是唯一的。比如`width`和`height`相对于父元素的`width`和`height`，而`margin`、`padding`不管垂直还是水平方向都相对比父元素的宽度、`border-radius`则是相对于元素自身等等，造成我们使用百分比单位容易使布局问题变得复杂。

#### 3.rem布局

`REM`是`CSS3`新增的单位，并且移动端的支持度很高，Android2.x+,ios5+都支持。`rem`单位都是相对于根元素html的`font-size`来决定大小的,根元素的`font-size`相当于提供了一个基准，当页面的size发生变化时，只需要改变`font-size`的值，那么以`rem`为固定单位的元素的大小也会发生响应的变化。 因此，如果通过`rem`来实现响应式的布局，只需要根据视图容器的大小，动态的改变`font-size`即可（而`em`是相对于父元素的）。

**rem响应式的布局思想：**

- 一般不要给元素设置具体的宽度，但是对于一些小图标可以设定具体宽度值
- 高度值可以设置固定值，设计稿有多大，我们就严格有多大
- 所有设置的固定值都用`rem`做单位（首先在HTML总设置一个基准值：`px`和`rem`的对应比例，然后在效果图上获取`px`值，布局的时候转化为`rem`值)
- js获取真实屏幕的宽度，让其除以设计稿的宽度，算出比例，把之前的基准值按照比例进行重新的设定，这样项目就可以在移动端自适应了

**rem布局的缺点：**

在响应式布局中，必须通过js来动态控制根元素`font-size`的大小，也就是说css样式和js代码有一定的耦合性，且必须将改变`font-size`的代码放在`css`样式之前

#### 4.视口单位

`css3`中引入了一个新的单位`vw/vh`，与视图窗口有关，`vw`表示相对于视图窗口的宽度，`vh`表示相对于视图窗口高度，除了`vw`和`vh`外，还有`vmin`和`vmax`两个相关的单位。

##### 搭配vw和rem

虽然采用`vw`适配后的页面效果很好，但是它是利用视口单位实现的布局，依赖视口大小而自动缩放，无论视口过大还是过小，它也随着时候过大或者过小，失去了最大最小宽度的限制，此时我们可以结合`rem`来实现布局

- 给根元素大小设置随着视口变化而变化的`vw`单位，这样就可以实现动态改变其大小
- 限制根元素字体大小的最大最小值，配合`body`加上最大宽度和最小宽度

### 8.图片响应式

这里的图片响应式包括两个方面，一个就是大小自适应，这样能够保证图片在不同的屏幕分辨率下出现压缩、拉伸的情况；一个就是根据不同的屏幕分辨率和设备像素比来尽可能选择高分辨率的图片，也就是当在小屏幕上不需要高清图或大图，这样我们用小图代替，就可以减少网络带宽了

#### 1.使用max-width（图片自适应）:

图片自适应意思就是图片能随着容器的大小进行缩放，可以采用如下代码：

```css
img {
    display: inline-block;
    max-width: 100%;
    height: auto;
}

```

`inline-block` 元素相对于它周围的内容以内联形式呈现，但与内联不同的是，这种情况下我们可以设置宽度和高度。 `max-width`保证了图片能够随着容器的进行等宽扩充（即保证所有图片最大显示为其自身的 100%。此时，如果包含图片的元素比图片固有宽度小，图片会缩放占满最大可用空间），而`height`为`auto`可以保证图片进行等比缩放而不至于失真。如果是背景图片的话要灵活运用`background-size`属性。

那么为什么不能用`width：100%`呢？因为这条规则会导致它显示得跟它的容器一样宽。在容器比图片宽得多的情况下，图片会被无谓地拉伸。

#### 2.使用srcset

```css
<img srcset="photo_w350.jpg 1x, photo_w640.jpg 2x" src="photo_w350.jpg" alt="">
```

如果屏幕的dpi = 1的话则加载1倍图，而dpi = 2则加载2倍图，手机和mac基本上dpi都达到了2以上，这样子对于普通屏幕来说不会浪费流量，而对于视网膜屏来说又有高清的体验。

如果浏览器不支持`srcset`，则默认加载src里面的图片。

但是你会发现实际情况并不是如此，在Mac上的Chrome它会同时加载`srcset`里面的那张2x的，还会再去加载src里面的那张，加载两张图片。顺序是先把所有`srcset`里面的加载完了，再去加载src的。这个策略比较奇怪，它居然会加载两张图片，如果不写src，则不会加载两张，但是兼容性就没那么好。这个可能是因为浏览器认为，既然有`srcset`就不用写src了，如果写了src，用户可能是有用的。而使用`picture`就不会加载两张

#### 3.使用background-image

```css
.banner{
  background-image: url(/static/large.jpg);
}

@media screen and (max-width: 767px){
  background-image: url(/static/small.jpg);
}
```

#### 4.使用picture标签

[picturefill.min.js](https://link.juejin.im/?target=https%3A%2F%2Fscottjehl.github.io%2Fpicturefill%2F) ：解决IE等浏览器不支持 的问题

```
<picture>
    <source srcset="banner_w1000.jpg" media="(min-width: 801px)">
    <source srcset="banner_w800.jpg" media="(max-width: 800px)">
    <img src="banner_w800.jpg" alt="">
</picture>

<!-- picturefill.min.js 解决IE等浏览器不支持 <picture> 的问题 -->
<script type="text/javascript" src="js/vendor/picturefill.min.js"></script>
```

`picture`必须要写img标签，否则无法显示，对`picture`的操作最后都是在img上面，例如onload事件是在img标签触发的，`picture`和`source`是不会进行layout的，它们的宽和高都是0。

另外使用`source`，还可以对图片格式做一些兼容处理：

```
<picture>
    <source type="image/webp" srcset="banner.webp">
    <img src="banner.jpg" alt="">
</picture>
```

**总结**：响应式布局的实现可以通过媒体查询+`px`,媒体查询+百分比，媒体查询+`rem`+`js`,`vm/vh`,`vm/vh` +`rem`这几种方式来实现。但每一种方式都是有缺点的，媒体查询需要选取主流设备宽度尺寸作为断点针对性写额外的样式进行适配，但这样做会比较麻烦，只能在选取的几个主流设备尺寸下呈现完美适配，另外用户体验也不友好，布局在响应断点范围内的分辨率下维持不变，而在响应断点切换的瞬间，布局带来断层式的切换变化，如同卡带的唱机般“咔咔咔”地一下又一下。通过百分比来适配首先是计算麻烦，第二各个属性中如果使用百分比，其相对的元素的属性并不是唯一的，这样就造成我们使用百分比单位容易使布局问题变得复杂。通过采用`rem`单位的动态计算的弹性布局，则是需要在头部内嵌一段脚本来进行监听分辨率的变化来动态改变根元素字体大小，使得`CSS`与`JS` 耦合了在一起。通过利用纯`css`视口单位实现适配的页面，是既能解决响应式断层问题，又能解决脚本依赖的问题的，但是兼容性还没有完全能结构接受

在实际项目中，我们可能需要综合上面的方案，比如用`rem`来做字体的适配，用`srcset`来做图片的响应式，宽度可以用`rem`，`flex`，栅格系统等来实现响应式，然后可能还需要利用媒体查询来作为响应式布局的基础，因此综合上面的实现方案，项目中实现响应式布局需要注意下面几点：

- 设置viewport
- 媒体查询
- 字体的适配（字体单位）
- 百分比布局
- 图片的适配（图片的响应式）
- 结合flex，grid，BFC，栅格系统等已经成型的方案

### 9.div 高度永远是宽度的一半

```html
	<style>
	*{
			margin: 0;
			padding: 0;
		}
		html,body{
			width:100%;
			height:100%;
     }
		.outer{
			width: 400px;
			height: 100%;
			background-color: blue;
			margin: 0 auto;
			display: flex;
			align-items: center;
		}
		.inner{
			position:relative;
			width:100%;
			height: 0;
			padding-bottom: 50%;
			background-color: red;
		}
		.box{
			position:absolute;
			width: 100%;
			height: 100%;
			display: flex;
			justify-content: center;
			align-items: center;
		}

	</style>
<body>
	<div class="outer">
		<div class="inner">
		<div class="box"> hello</div>
		</div>
	</div>
</body>
```

### 10.对齐实现

#### 居中对齐元素

要使块元素（例如 <div> ）水平居中，请使用 `margin: auto;`。

设置元素的宽度将防止其延伸到容器的边缘。

然后，元素将占用指定的宽度，剩余空间将在两个外边距之间平均分配

如果未设置 **width** 属性（或将其设置为 100％），则居中对齐无效

#### 居中对齐文本

如果仅需在元素内居中文本，请使用 `text-align: center`

#### 居中对齐图像

如需居中图像，请将左右外边距设置为 auto，并将其设置为块元素

```css
img {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 40%;
}
```

#### 左和右对齐 - 使用 position

对齐元素的一种方法是使用 position: absolute

#### 左和右对齐 - 使用 float

对齐元素的另一种方法是使用 float 属性

#### 垂直对齐 - 使用 padding

设置上下padding  都一样   如`padding: 100px 0;` 如需同时垂直和水平对齐，请使用 padding 和 text-align: center

#### 垂直对齐 - 使用 line-height

使用其值等于 height 属性值的 line-height 属性：

```css
.center {
  line-height: 200px;
  height: 200px;
  border: 3px solid green;
  text-align: center;
}

/* 如果有多行文本，请添加如下代码：*/
.center p {
  line-height: 1.5;
  display: inline-block;
  vertical-align: middle;
}
```

#### 垂直对齐 - 使用 Flexbox

```css
.center {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 200px;
  border: 3px solid green; 
}
```

### 11.行内元素布局

-   line-height的数字值是和font-size大小相关的；
-   vertical-align的百分比值是和line-height值相关的；

#### font-size

font-size 属性可设置字体的尺寸  该属性设置元素的字体大小。注意，实际上它设置的是字体中字符框的高度；实际的字符字形可能比这些框高或矮（通常会矮）

#### line-height

`行高`，顾名思义指的就是一行文字的高度。**按照定义来解释，就是两行文字之间基线之间的距离**

**行高就是两条基线的之间的距离**，如下图所示。



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/6/8/163dcd21b06c977f~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



-   两条红线之间的距离就是`行高（line-height）`。
-   上一行的底线和下一行的顶线之间的距离就是`行距`，业界的共识是：行距=行高-em-box（暂时理解为font-size的大小），因此，用CSS语言来解释行距就是： 行距=line-height-font-size。
-   同一行顶线和底线之间的距离就是`font-size`。
-   行距的一半就是`半行距`。

 内容区、行内框、行框、包含框

所谓一图胜千言：



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/6/8/163dcd218984d6e9~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)



**内容区**：内容区域可以近似理解为FireFox/IE浏览器下文本选中带背景的区域，在上图中，深灰色背景区域就是内容区域。

**行内框**：每一个行内元素都会生成一个行内框，高度等于`font-size`，当我们设置`line- `的时候，行内框的高度保持不变，改变的是行距的高度。

**行框**：指本行的一个虚拟的矩形框，由本行中的行内框组成。当有多行内容的时候，每一行都有自己的的一个行框。

**包含框**： 包裹着上述三种box的box，晕了，直接看图吧，上面黄颜色的框就是包含框。

**行内元素自身并没有行高这个属性，虽然设置可以设置行高，但是最终作用的地方并不是自身，而是其自身所在的行框盒子上！**

![img](https://img2018.cnblogs.com/i-beta/1550425/201912/1550425-20191206102536467-480079696.png)

 

 

 结论是：

　　**内联盒子没有行高这一属性，给它设置line-height，最终是作用在行框盒子上，而行框盒子最终会取内部行高最高的那个作为最终行高，而此行高将在其内部所有内联盒子中生效**

line-height的默认值是normal，同时还支持数值、百分比值、长度值、继承。请看下面的表格：

| 值      | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| normal  | 默认。设置合理的行间距。                                     |
| number  | 设置数字，此数字会与当前的字体尺寸相乘来设置行间距，即number为当前font-size的倍数。 |
| length  | 设置固定的行间距。                                           |
| %       | 基于当前字体尺寸的百分比行间距。                             |
| inherit | 规定应该从父元素继承 line-height 属性的值。                  |

#### vertical-align



| 值          | 描述                                     |
| ----------- | ---------------------------------------- |
| baseline    | 默认。元素放置在父元素的基线上。         |
| top         | 把元素的顶端与行中最高元素的顶端对齐     |
| text-top    | 把元素的顶端与父元素字体的顶端对齐       |
| middle      | 把此元素放置在父元素的中部。             |
| bottom      | 把元素的顶端与行中最低的元素的顶端对齐。 |
| text-bottom | 把元素的底端与父元素字体的底端对齐。     |

-   baseline 为 vertical-align 的默认值，其意思是指基线对齐

    

    ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/8/170ba4210376fdef~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

    

我们可以把每一个行框盒子的后面想象有一个看不见的节点 x（该节点继承了 line-height ），因为默认对齐方式为基线对齐，所以`.text` 就是和这个字母 x 的下边缘对齐

-   top 与 bottom

>   对于内联元素，指的是元素的顶部（底部）和当前行框盒子的顶部（底部）对齐；即与 line-box 的顶部（底部）对齐 对于 table-cell 元素，指的是元素的顶 padding 边缘和表格行的顶部对齐。

-   middle 这个属性值用得比较多。

>   对于内联元素指的是元素的垂直中心点与行框盒子基线往上 1/2x-height 处对齐，简单点说就是字母 X 的中心位置对齐；对于 table-cell 元素，指的是单元格填充盒子相对于外面的表格行居中对齐。

### 12.grid布局

## Grid 布局是什么？

`Grid` 布局即网格布局，是一种新的 `CSS` 布局模型，比较擅长将一个页面划分为几个主要区域，以及定义这些区域的大小、位置、层次等关系。号称是最强大的的 `CSS` 布局方案，是目前唯一一种 `CSS` 二维布局。利用 `Grid` 布局，我们可以轻松实现类似下图布局，[演示地址](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fgpingfeng%2Fpen%2FqBbveKB%3Feditors%3D1100)



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/26/17389591885783dd~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



## Grid 布局和 flex 布局

讲到布局，我们就会想到 `flex` 布局，甚至有人认为竟然有 `flex` 布局了，似乎没有必要去了解 `Grid` 布局。但 `flex` 布局和 `Grid` 布局有实质的区别，那就是 **`flex` 布局是一维布局，`Grid` 布局是二维布局**。`flex` 布局一次只能处理一个维度上的元素布局，一行或者一列。`Grid` 布局是将容器划分成了“行”和“列”，产生了一个个的网格，我们可以将网格元素放在与这些行和列相关的位置上，从而达到我们布局的目的。

**`Grid` 布局远比 `flex` 布局强大！**

flex布局示例:



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/28/173945aadff842d1~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



Grid 布局示例：



![Grid 布局](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/26/173895918bcb5190~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

容器和项目：我们通过在元素上声明 `display：grid` 或 `display：inline-grid` 来创建一个网格容器。一旦我们这样做，这个元素的所有直系子元素将成为网格项目。比如上面 `.wrapper` 所在的元素为一个网格容器，其直系子元素将成为网格项目。

网格轨道：`grid-template-columns` 和 `grid-template-rows` 属性来定义网格中的行和列。容器内部的水平区域称为行，垂直区域称为列。上图中 `One`、`Two`、`Three` 组成了一行，`One`、`Four` 则是一列

网格单元：一个网格单元是在一个网格元素中最小的单位， 从概念上来讲其实它和表格的一个单元格很像。上图中 `One`、`Two`、`Three`、`Four`...都是一个个的网格单元

网格线：划分网格的线，称为"网格线"。应该注意的是，当我们定义网格时，我们定义的是网格轨道，而不是网格线。Grid 会为我们创建编号的网格线来让我们来定位每一个网格元素。m 列有 m + 1 根垂直的网格线，n 行有 n + 1 跟水平网格线。比如上图示例中就有 4 根垂直网格线。一般而言，是从左到右，从上到下，1，2，3 进行编号排序。当然也可以从右到左，从下到上，按照 -1，-2，-3...顺序进行编号排序

**repeat() 函数**：可以简化重复的值。该函数接受两个参数，第一个参数是重复的次数，第二个参数是所要重复的值。比如上面行高都是一样的，我们可以这么去实现，实际效果是一模一样的

```
.wrapper-1 {
  display: grid;
  grid-template-columns: 200px 100px 200px;
  grid-gap: 5px;
  /*  2行，而且行高都为 50px  */
  grid-template-rows: repeat(2, 50px);
}
```

**auto-fill 关键字**：表示自动填充，让一行（或者一列）中尽可能的容纳更多的单元格。`grid-template-columns: repeat(auto-fill, 200px)` 表示列宽是 200 px，但列的数量是不固定的，只要浏览器能够容纳得下，就可以放置元素，代码以及效果如下图所示：

```
.wrapper-2 {
  display: grid;
  grid-template-columns: repeat(auto-fill, 200px);
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

**fr 关键字**：`Grid` 布局还引入了一个另外的长度单位来帮助我们创建灵活的网格轨道。`fr` 单位代表网格容器中可用空间的一等份。`grid-template-columns: 200px 1fr 2fr` 表示第一个列宽设置为 200px，后面剩余的宽度分为两部分，宽度分别为剩余宽度的 1/3 和 2/3。代码以及效果如下图所示：

```
.wrapper-3 {
  display: grid;
  grid-template-columns: 200px 1fr 2fr;
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

**minmax() 函数**：我们有时候想给网格元素一个最小和最大的尺寸，`minmax()` 函数产生一个长度范围，表示长度就在这个范围之中都可以应用到网格项目中。它接受两个参数，分别为最小值和最大值。`grid-template-columns: 1fr 1fr minmax(300px, 2fr)` 的意思是，第三个列宽最少也是要 300px，但是最大不能大于第一第二列宽的两倍。代码以及效果如下：

```
.wrapper-4 {
  display: grid;
  grid-template-columns: 1fr 1fr minmax(300px, 2fr);
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

**auto 关键字**：由浏览器决定长度。通过 `auto` 关键字，我们可以轻易实现三列或者两列布局。`grid-template-columns: 100px auto 100px` 表示第一第三列为 100px，中间由浏览器决定长度，代码以及效果如下：

```
.wrapper-5 {
  display: grid;
  grid-template-columns: 100px auto 100px;
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

### grid-row-gap 属性、grid-column-gap 属性以及 grid-gap 属性

[grid-row-gap 属性、grid-column-gap 属性以及 grid-gap 属性演示地址](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fgpingfeng%2Fpen%2FjOWRNeg)

`grid-row-gap` 属性、`grid-column-gap` 属性分别设置行间距和列间距。`grid-gap` 属性是两者的简写形式。

`grid-row-gap: 10px` 表示行间距是 10px，`grid-column-gap: 20px` 表示列间距是 20px。`grid-gap: 10px 20px` 实现的效果是一样的

### justify-items 属性、align-items 属性以及 place-items 属性

[justify-items 属性、align-items 属性演示地址](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fgpingfeng%2Fpen%2FzYrXYrz%3Feditors%3D1100)

`justify-items` 属性设置单元格内容的水平位置（左中右），`align-items` 属性设置单元格的垂直位置（上中下）

下面以 justify-items 属性为例进行讲解，align-items 属性同理，只是方向为垂直方向。它们都有如下属性：

```
.container {
  justify-items: start | end | center | stretch;
  align-items: start | end | center | stretch;
}
```

其代码实现以及效果如下：

```
.wrapper, .wrapper-1, .wrapper-2, .wrapper-3 {
  display: grid;
  grid-template-columns: 100px 200px 100px;
  grid-gap: 5px;
  grid-auto-rows: 50px;
  justify-items: start;
}
.wrapper-1 {
  justify-items: end;
}
.wrapper-2 {
  justify-items: center;
}
.wrapper-3 {
  justify-items: stretch;
}
```

### justify-content 属性、align-content 属性以及 place-content 属性

[justify-content 属性、align-content 属性演示地址](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fgpingfeng%2Fpen%2FqBbwBZx%3Feditors%3D1100)

`justify-content` 属性是整个内容区域在容器里面的水平位置（左中右），`align-content` 属性是整个内容区域的垂直位置（上中下）。它们都有如下的属性值。

```
.container {
  justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
  align-content: start | end | center | stretch | space-around | space-between | space-evenly;  
}
```

下面以 `justify-content` 属性为例进行讲解，`align-content` 属性同理，只是方向为垂直方向

- start - 对齐容器的起始边框
- end - 对齐容器的结束边框
- center - 容器内部居中

```
.wrapper, .wrapper-1, .wrapper-2, .wrapper-3, .wrapper-4, .wrapper-5, .wrapper-6 {
  display: grid;
  grid-template-columns: 100px 200px 100px;
  grid-gap: 5px;
  grid-auto-rows: 50px;
  justify-content: start;
}
.wrapper-1 {
  justify-content: end;
}
.wrapper-2 {
  justify-content: center;
}
```

## Grid 实战——实现响应式布局

经过上面的介绍，相信大家都可以看出，Grid 是非常强大的。一些常见的 CSS 布局，如居中，两列布局，三列布局等等是很容易实现的。我们接下来看看 Grid 布局是如何实现响应式布局的

### fr 实现等分响应式

[fr 实现等分响应式](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fgpingfeng%2Fpen%2FwvMZKpB%3Feditors%3D1100)

`fr` 等分单位，可以将容器的可用空间分成想要的多个等分空间。利用这个特性，我们能够轻易实现一个等分响应式。`grid-template-columns: 1fr 1fr 1fr` 表示容器分为三等分

```
.wrapper {
  margin: 50px;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-gap: 10px 20px;
  grid-auto-rows: 50px;
}
```



![image](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/26/17389592bf7e44dd~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



### repeat + auto-fit——固定列宽，改变列数量

等分布局并不只有 `Grid` 布局才有，像 `flex` 布局也能轻松实现，接下来看看更高级的响应式

上面例子的始终都是三列的，但是需求往往希望我们的网格能够固定列宽，并根据容器的宽度来改变列的数量。这个时候，我们可以用到上面提到 `repeat()` 函数以及 `auto-fit` 关键字。`grid-template-columns: repeat(auto-fit, 200px)` 表示固定列宽为 200px，数量是自适应的，只要容纳得下，就会往上排列，代码以及效果实现如下：

[演示地址](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fgpingfeng%2Fpen%2FeYJopVE%3Feditors%3D1100)

```
.wrapper {
  margin: 50px;
  display: grid;
  grid-template-columns: repeat(auto-fit, 200px);
  grid-gap: 10px 20px;
  grid-auto-rows: 50px;
}
```



![image](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/26/17389592c297495a~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



### repeat+auto-fit+minmax 去掉右侧空白

上面看到的效果中，右侧通常会留下空白，这是我们不希望看到的。如果列的宽度也能在某个范围内自适应就好了。`minmax()` 函数就帮助我们做到了这点。将 `grid-template-columns: repeat(auto-fit, 200px)` 改成 `grid-template-columns: repeat(auto-fit, minmax(200px, 1fr))` 表示列宽至少 200px，如果还有空余则一起等分。代码以及效果如下所示：

[演示地址](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fgpingfeng%2Fpen%2FdyGLYdQ)

```
.wrapper {
  margin: 50px;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  grid-gap: 10px 20px;
  grid-auto-rows: 50px;
}
```



![auto-auto-minmax.gif](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/26/17389592cc3c2bf9~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



### repeat+auto-fit+minmax-span-dense 解决空缺问题

似乎一切进行得很顺利，但是某天 UI 来说，每个网格元素的长度可能不相同，这也简单，通过 `span` 关键字进行设置网格项目的跨度，`grid-column-start: span 3`，表示这个网格项目跨度为 3。具体的代码与效果如下所示：

```
.item-3 {
  grid-column-start: span 3;
}
```

[演示地址](https://link.juejin.cn?target=https%3A%2F%2Fcodepen.io%2Fgpingfeng%2Fpen%2FBajEoxy%3Feditors%3D1100)



![image](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/26/17389592f9da3763~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



不对，怎么右侧又有空白了？原来是有一些长度太长了，放不下，这个时候就到我们的 `dense` 关键字出场了。`grid-auto-flow: row dense` 表示尽可能填充，而不留空白，代码以及效果如下所示：

```
.wrapper, .wrapper-1 {
  margin: 50px;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  grid-gap: 10px 20px;
  grid-auto-rows: 50px;
}

.wrapper-1 {
  grid-auto-flow: row dense;
}
```



![image](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/26/17389593009f7fe7~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



## Grid 布局兼容性

最后，聊聊 `Grid` 布局兼容性问题，在 [caniuse](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%23search%3Dgrid) 中，我们可以看到的结果如下，总体兼容性还不错，但在 IE 10 以下不支持。个人建议在公司的内部系统运用起来是没有问题的，但 TOC 的话，可能目前还是不太合适



![image](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/26/17389592fa541366~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



## 场景应用

### 1.画0.5px的线

- **采用transform: scale()的方式**，该方法用来定义元素的2D 缩放转换：

```css
transform: scale(0.5,0.5);
```

- **采用meta viewport的方式**

```css
<meta name="viewport" content="width=device-width, initial-scale=0.5, minimum-scale=0.5, maximum-scale=0.5"/>
```

这样就能缩放到原来的0.5倍，如果是1px那么就会变成0.5px。viewport只针对于移动端，只在移动端上才能看到效果

### 2.画三角形扇形正方形

CSS绘制三角形主要用到的是border属性，也就是边框。

平时在给盒子设置边框时，往往都设置很窄，就可能误以为边框是由矩形组成的。实际上，border属性是右三角形组成的，下面看一个例子：

```css
div {
    width: 0;
    height: 0;
    border: 100px solid;
    border-color: orange blue red green;
}
```

将元素的长宽都设置为0，显示出来的效果是这样的： ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cba8731fea9842a8b8103c2b387fe64f~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

```css
div {
    width: 0;
    height: 0;
    border-bottom: 50px solid red;
    border-right: 50px solid transparent;
    border-left: 50px solid transparent;
}
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/531c6c250dd8446fb0f264e7b3df6fba~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

用CSS实现扇形的思路和三角形基本一致，就是多了一个圆角的样式，实现一个90°的扇形：

```css
div{
    border: 100px solid transparent;
    width: 0;
    heigt: 0;
    border-radius: 100px;
    border-top-color: red;
}
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/db5e46aea0ce4805a0c2bbec2743546e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 3.解决1px问题

1px 问题指的是：在一些 `Retina屏幕` 的机型上，移动端页面的 1px 会变得很粗，呈现出不止 1px 的效果。原因很简单——CSS 中的 1px 并不能和移动设备上的 1px 划等号。它们之间的比例关系有一个专门的属性来描述：

```html
window.devicePixelRatio = 设备的物理像素 / CSS像素。
```

打开 Chrome 浏览器，启动移动端调试模式，在控制台去输出这个 `devicePixelRatio` 的值。这里选中 iPhone6/7/8 这系列的机型，输出的结果就是2： ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a8af7dca29f84b7e9d1f94232713ef07~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) 这就意味着设置的 1px CSS 像素，在这个设备上实际会用 2 个物理像素单元来进行渲染，所以实际看到的一定会比 1px 粗一些。 **解决1px 问题的三种思路：**

#### 思路一：直接写 0.5px

如果之前 1px 的样式这样写：

```css
border:1px solid #333
```

可以先在 JS 中拿到 window.devicePixelRatio 的值，然后把这个值通过 JSX 或者模板语法给到 CSS 的 data 里，达到这样的效果（这里用 JSX 语法做示范）：

```javascript
<div id="container" data-device={{window.devicePixelRatio}}></div>
```

然后就可以在 CSS 中用属性选择器来命中 devicePixelRatio 为某一值的情况，比如说这里尝试命中 devicePixelRatio 为2的情况：

```css
#container[data-device="2"] {
  border:0.5px solid #333
}
```

直接把 1px 改成 1/devicePixelRatio 后的值，这是目前为止最简单的一种方法。这种方法的缺陷在于兼容性不行，IOS 系统需要8及以上的版本，安卓系统则直接不兼容。

#### 思路二：伪元素先放大后缩小

这个方法的可行性会更高，兼容性也更好。唯一的缺点是代码会变多。

思路是**先放大、后缩小：在目标元素的后面追加一个 ::after 伪元素，让这个元素布局为 absolute 之后、整个伸展开铺在目标元素上，然后把它的宽和高都设置为目标元素的两倍，border值设为 1px。接着借助 CSS 动画特效中的放缩能力，把整个伪元素缩小为原来的 50%。此时，伪元素的宽高刚好可以和原有的目标元素对齐，而 border 也缩小为了 1px 的二分之一，间接地实现了 0.5px 的效果。**

代码如下：

```css
#container[data-device="2"] {
    position: relative;
}
#container[data-device="2"]::after{
      position:absolute;
      top: 0;
      left: 0;
      width: 200%;
      height: 200%;
      content:"";
      transform: scale(0.5);
      transform-origin: left top;
      box-sizing: border-box;
      border: 1px solid #333;
    }
}
```

#### 思路三：viewport 缩放来解决

这个思路就是对 meta 标签里几个关键属性下手：

```html
<meta name="viewport" content="initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no">
```

这里针对像素比为2的页面，把整个页面缩放为了原来的1/2大小。这样，本来占用2个物理像素的 1px 样式，现在占用的就是标准的一个物理像素。根据像素比的不同，这个缩放比例可以被计算为不同的值，用 js 代码实现如下：

```javascript
const scale = 1 / window.devicePixelRatio;
// 这里 metaEl 指的是 meta 标签对应的 Dom
metaEl.setAttribute('content', `width=device-width,user-scalable=no,initial-scale=${scale},maximum-scale=${scale},minimum-scale=${scale}`);
```

这样解决了，但这样做的副作用也很大，整个页面被缩放了。这时 1px 已经被处理成物理像素大小，这样的大小在手机上显示边框很合适。但是，一些原本不需要被缩小的内容，比如文字、图片等，也被无差别缩小掉了。

### 4.设置小于12px的字体

### 如何让Chrome支持小于12px的文字？

谷歌浏览器默认最小字体为12px，小于12px的字体它都以12px显示。

解决方法大致如下：

- 设置-webkit-text-size-adjust:none;（最新chrome已不支持）
- 设置-webkit-transform:scale(0.8);

```css
.chrome_adjust{
    font-size: 10px; //针对可以识别12px以下字体大小的浏览器
    -webkit-transform: scale(0.8);
    -webkit-transform-origin-X: left; //定义文字相对于X轴的位置
    -o-transform: scale(1); //针对能识别-webkit的opera browser设置  
    display: inline-block;
}

//至于为什么要设置display:inline-block； 因为scale属性只对可以定义宽高的元素有效。
```

### 5.实现宽高自适应

- 利用vw来实现：

```css
.square {
  width: 10%;
  height: 10vw;
  background: tomato;
}
```

- 利用元素的margin/padding百分比是相对父元素width的性质来实现：

```css
.square {
  width: 20%;
  height: 0;
  padding-top: 20%;
  background: orange;
}
```

- 利用子元素的margin-top的值来实现：

```css
.square {
  width: 30%;
  overflow: hidden;
  background: yellow;
}
.square::after {
  content: '';
  display: block;
  margin-top: 100%;
}
```

### 6.实现朋友圈的图片排布

[纯css实现朋友圈照片排列布局](https://juejin.cn/post/6844904184941117454)

**假设有如下HTML代码，**

这里imgList是一个图片地址数组；

------

```
<ul>
    <li v-for="(item, index) in imgList" :key=index >
        <img :src="item">
    </li>
</ul>
```

1、首先我们使用flex实现正常的三列布局:

设置为换行，每个元素占1/3或指定宽度，除每行最后一个元素（3n）都设置margin-right并通过预留间隔；

```
ul{
  display: flex;
  justify-content: flex-start;
  flex-wrap: wrap;
}
li{
  width: 32%; 
  height: 100px;
  margin-top: 5px;
}
.list:not(:nth-child(3n)) {
  margin-right: 2%;
}
```

2、对于只有一张图片情况，只需用css选择器判断为一张图片时，改变图片大小即可;

**选择器逻辑：元素为 倒数第一个元素 && 第一个元素 时，则可判断只有一个元素：对其样式单独设置覆盖原样式即可**

```
ul li: nth-last-child(1): first-child{
  width: 200px;
  height: 200px;
}
```

3、对于四张图片的情况时，图片需呈 两行两列布局：这里就需对此种情况下的第二张图片添加margin-right实现三列变两列：

**选择器逻辑： 元素为 倒数第4个 && 第一个的元素 时， 判断为共有四个元素，**

 **再选择 其后的 同级元素 的第 2n 个后添加margin-right属性；**

```
ul li: nth-last-child(4): first-child ~ li: nth-child(2n){
  margin-right: 32%;
}
```

再次之前需对第三个元素恢复间隔，或同朋友圈类似，四张照片是不显示间隔，如有需求也可设置其他属性，如下：（此属性需在上一条属性之前）

```
ul li: nth-last-child(4): first-child , ul li: nth-last-child(4): first-child ~ li{
  width: 50%;
  margin-right: 0;
}
```

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/179f32c46fc94cf6b79f31cecbd5d126~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### 7.图片下方出现多3px的原因及解决方法

产生原因：主要是因为图片的垂直对齐方式vertical-align引发的，默认值是baseline，默认为此值时图片下方就会多出3px。

解决方案：

1.将图片的垂直对齐方式vertical-align的值设置为bottom，就可以解决这个问题。

2.将图片display设置为block，并且指定width和height。

3.设置图片所在的容器元素和width和height与图片一样。

### 8.flex实现骰子布局

```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>flex</title>
  <style>
    .bigbox {
      display: flex;
      justify-content: center;
      align-items: center;
      width: 600px;
      height: 600px;
      background-color: orange;
      margin: 0 auto;
    }
    
    .box1 {
      height: 50px;
      width: 50px;
      background-color: #fff;
      padding: 5px;
      display: flex;
      border-radius: 10px;
      justify-content: center;
      align-items: center;
    }
    
    .one {
      background-color: red;
      width: 18px;
      height: 18px;
      border-radius: 9px;
    }
    
    .box2 {
      display: flex;
      height: 50px;
      width: 50px;
      background-color: #fff;
      padding: 5px;
      margin-left: 5px;
      border-radius: 10px;
      justify-content: space-between;
    }
    
    .two1 {
      background-color: black;
      width: 15px;
      height: 15px;
      border-radius: 7.5px;
      align-self: flex-start;
    }
    
    .two2 {
      background-color: black;
      width: 15px;
      height: 15px;
      border-radius: 7.5px;
      align-self: flex-end;
    }
    
    .box3 {
      display: flex;
      height: 50px;
      width: 50px;
      background-color: #fff;
      padding: 5px;
      margin-left: 5px;
      border-radius: 10px;
      align-items: center;
      justify-content: space-evenly;
    }
    
    .three {
      background-color: black;
      width: 12px;
      height: 12px;
      border-radius: 6px;
    }
    
    .three:nth-child(1) {
      align-self: flex-start;
    }
    
    .three:nth-child(3) {
      align-self: flex-end;
    }
    
    .box4 {
      display: flex;
      height: 50px;
      width: 50px;
      background-color: #fff;
      padding: 5px;
      margin-left: 5px;
      border-radius: 10px;
      justify-content: space-between;
    }
    
    .four {
      background-color: black;
      width: 15px;
      height: 15px;
      border-radius: 7.5px;
    }
    
    .culmn {
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }
    
    .box5 {
      display: flex;
      height: 50px;
      width: 50px;
      background-color: #fff;
      padding: 5px;
      margin-left: 5px;
      border-radius: 10px;
      justify-content: space-between;
    }
    
    .five {
      background-color: black;
      width: 15px;
      height: 15px;
      border-radius: 7.5px;
    }
    
    .center {
      width: 17px;
      height: 17px;
      border-radius: 8.5px;
      background-color: red;
      align-self: center;
    }
    
    .culmn5 {
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }
    
    .box6 {
      display: flex;
      height: 50px;
      width: 50px;
      background-color: #fff;
      padding: 5px;
      margin-left: 5px;
      border-radius: 10px;
      justify-content: space-around;
    }
    
    .six {
      background-color: black;
      width: 15px;
      height: 15px;
      border-radius: 7.5px;
    }
    
    .culmn6 {
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }
  </style>
</head>

<body>
  <div class="bigbox">
    <div class="box1">
      <div class="one"></div>
    </div>

    <div class="box2">
      <div class="two1"></div>
      <div class="two2"></div>
    </div>

    <div class="box3">
      <div class="three"></div>
      <div class="three"></div>
      <div class="three"></div>
    </div>

    <div class="box4">
      <div class="culmn">
        <div class="four"></div>
        <div class="four"></div>
      </div>
      <div class="culmn">
        <div class="four"></div>
        <div class="four"></div>
      </div>
    </div>

    <div class="box5">
      <div class="culmn5">
        <div class="five"></div>
        <div class="five"></div>
      </div>
      <div class="five center"></div>
      <div class="culmn5">
        <div class="five"></div>
        <div class="five"></div>
      </div>
    </div>

    <div class="box6">
      <div class="culmn6">
        <div class="six"></div>
        <div class="six"></div>
        <div class="six"></div>
      </div>
      <div class="culmn6">
        <div class="six"></div>
        <div class="six"></div>
        <div class="six"></div>
      </div>
    </div>
  </div>
</body>

</html>

```

![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f661cee991614727b1a2b2f5148f1fa7~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 9.实现九宫格

## 基本布局和样式

```html
<div class="box">
    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
        <li>5</li>
        <li>6</li>
        <li>7</li>
        <li>8</li>
        <li>9</li>
    </ul>
</div>
.box {
    background: #e4f7fd61;
    border: 2px solid #0786ada1;
    border-radius: 8px;
}

ul {
    padding: 0; 
    margin: 0;
}

.box li {
    list-style: none;
    text-align: center;
    line-height: 200px;
    background: skyblue;
    border-radius: 8px;
    font-size: 20px;
    color: black;
}
```

## 实现一：flex

使用 `flex` 布局实现需要注意一个点，就是需要用 `flex-wrap` 属性来使其换行。

```css
.box {
    width: 100%;
    overflow: hidden;
}

ul {
    width: 100%;
    height: 100%;
    display: flex;
    flex-wrap: wrap;
}

.box li {
    width: 30%;
    height: 30%;
    margin-right: 5%;
    margin-bottom: 5%;
}

.box li:nth-of-type(3n) {
    margin-right: 0;
}

.box li:nth-of-type(n+7) {
    margin-bottom: 0;
}
```

## 实现二：float

使用 `float` 来实现需要注意一个点，浮动会造成浮动崩塌，因此可以设置 `overflow: hidden;` 把 `box` 设置成 BFC 来解决浮动崩塌。

```css
.box {
    width: 100%;
    overflow: hidden;
}

ul {
    width: 100%;
    height: 100%;
}

.box li {
    width: 30%;
    height: 30%;
    margin-right: 5%;
    margin-bottom: 5%;
    float: left;
}

.box li:nth-of-type(3n) {
    margin-right: 0;
}

.box li:nth-of-type(n+7) {
    margin-bottom: 0;
}
```

## 实现三：grid

![图片.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7823dfac402f46c9a98d0e205846af7f~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

```html
<div class="grid">
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
    <div>5</div>
    <div>6</div>
    <div>7</div>
    <div>8</div>
    <div>9</div>
</div>
box {
    background: #e4f7fd61;
    border: 2px solid #0786ada1;
    border-radius: 8px;
}

.grid {
    display: grid;
    width: 100%;
    grid-template-rows: repeat(3, 1fr);
    grid-template-columns: repeat(3, 1fr);
    grid-gap: 5%;
    grid-auto-flow: row;
}

.grid>div {
    list-style: none;
    text-align: center;
    line-height: 200px;
    background: skyblue;
    border-radius: 8px;
    font-size: 20px;
    color: black;
}
```

## 实现四：table

使用表格来实现会存在一些缺陷，`table` 单元格之间的间隔是使用 `border-spacing` 属性来实现的，且**不支持百分比的形式**，而且单元格四周都有类似于 `margin` 的外边距的效果。

![图片.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e4231bb0a8374ebdb59d41e7a86441b8~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

```html
<div class="box">
    <ul>
        <li>
            <div>1</div>
            <div>2</div>
            <div>3</div>
        </li>
        <li>
            <div>4</div>
            <div>5</div>
            <div>6</div>
        </li>
        <li>
            <div>7</div>
            <div>8</div>
            <div>9</div>
        </li>
    </ul>
</div>
.box {
    width: 100%;
    overflow: hidden;
}

ul {
    width: 100%;
    height: 100%;
    display: table;
    border-spacing: 10px;
}

.box li {
    display: table-row;
}

.box li div {
    display: table-cell;
    text-align: center;
    border-radius: 10px;
}
```
