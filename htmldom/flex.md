# flex布局

Flexbox 布局（也叫Flex布局，弹性盒子布局）模块目标在于提供一个更有效地布局、对齐方式，并且能够使父元素在子元素的大小未知或动态变化情况下仍然能够分配好子元素之间的间隙。Flex布局的主要思想是使父元素能够调节子元素的高度、宽度和排布的顺序，从而能够最好地适应可用布局空间（能够适应不同的设备和不同大小的屏幕）。

> Flex 布局比较适合小规模的布局，性能消耗大，Gird布局面向更大规模的布局。

Flex布局是一个完整的模块而不是一个单独的属性，它包括了完整的一套属性。其中有的属性是设置在容器上，有的则是设置在容器的项目上。



###  父元素属性

####  display    

用来设置一个元素为flex布局容器，设置flex为块状元素，设置 `inline-flex` 父元素为行内元素，当元素设置为flex布局后，子元素的**子元素的 float clear  和 vertical-align 属性都将失效**



#### flex-direction

定义flex布局的主轴方向，flex布局是单方向的布局，子元素沿着水平布局或者垂直布局

```css
.container{
  flex-direction: row | row-reverse | column | column-reverse; //决定元素的排列的方向
}
row : 默认值，主轴为水平方向，起点在左端
row-reverse: 主轴为水平方向，起点在右端
column: 主轴为垂直方向，起点在上沿
column-reverse: 主轴为垂直方向，起点在下沿
```

#### flex-wrap

默认情况下，flex布局中元素会把子元素尽可能的排在同一行，通过该属性可以设置是否换行,而且子元素的弹性布局能力消失

```css
.container{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
nowrap: 不折行，默认值，所有的子元素会排在一行。
wrap: 折行，子元素会从上到下根据需求折成多行。
wrap-reverse: 从下向上折行，子元素会从下岛上根据需求折成多行。
```

#### flex-flow

`flex-flow` 是 `flex-direction`  和 `flex-wrap` 属性的缩写，默认值 `row, nowrap`

```css
flex-flow: flex-direction || flex-wrap
```

#### justify-content

定义了子元素沿主轴方向的对齐方式，用来当子元素大小最大的时候，分配主轴上的剩余空间。也可以当子元素超出主轴的时候用来控制子元素的对齐方式。

- `flex-start`: 默认值，朝主轴开始的方向对齐。
- `flex-end`: 朝主轴结束的方向对齐。
- `center`: 沿主轴方向居中。
- `space-between`: 沿主轴两端对齐，第一个子元素在主轴起点，最后一个子元素在主轴终点。
- `space-around`: 沿主轴子元素之间均匀分布。要注意的是子元素看起来间隙是不均匀的，第一个子元素和最后一个子元素离父元素的边缘有一个单位的间隙，但两个子元素之间有两个单位的间隙，因为每个子元素的两侧都有一个单位的间隙。

#### align-items

定义子元素在交叉轴方向的对齐方式，这是在每个子元素仍然在其原来所在行的基础上所说的。可以看作是交叉轴上的`justify-content`属性;

- flex-start: 按照交叉轴的起点对齐。
- flex-end: 按照交叉轴的终点对齐。
- center: 沿交叉轴方向居中。
- baseline: 按照项目的第一行文字的基线对齐。
- stretch: 默认值，在满足子项目所设置的`min-height`、`max-height`、`height`的情况下拉伸子元素使之填充整个父元素。



#### align-content

当父元素所包含的行在交叉轴方向有空余部分时如何分配空间。与`justify-content`在主轴上如何对单个子元素对齐很相似。

> 当只有一行时，该属性不起作用

```css
.container{
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

> 该属性中的六个属性值与`justify-content`中的六个属性意思相似，不同之处在于`justify-content`沿主轴方向的作用于单个子元素，而`align-content`沿交叉轴方向作用于行。
>
> 注意`align-items`和`align-content`的区别，前者是指在单行内的子元素对齐方式，后者是指多行之间的对齐方式。

####  父元素属性总结

```css
display: flex|inline-flex;
flex-direction: row | row-reverse | column | column-reverse;
flex-wrap: nowrap | wrap | wrap-reverse;
flex-flow: <‘flex-direction’> || <‘flex-wrap’>;
justify-content: flex-start | flex-end | center | space-between | space-around;
align-items: flex-start | flex-end | center | baseline | stretch;
align-content: flex-start | flex-end | center | space-between | space-around | stretch;
```



###  子元素属性

#### order

默认情况下，子元素按照代码书写的先后顺序布局，但`order`属性可以更改子元素出现的顺序。

#### flex-grow

`flex-grow` 规定在空间允许的情况下，子元素如何按照比例分配可用剩余空间。如果所有的子元素的属性都设定为`1`，则父元素中的剩余空间会等分给所有子元素。如果其中某个子元素的`flex-grow`设定为2，则在分配剩余空间时该子元素将获得其他元素二倍的空间。

> flex-grow 不接受负值，默认值为0

#### flex-shrink

与`flex-grow`属性类似，`flex-shrink`定义了空间不足时项目的缩小比例。

> 默认值为1， 空间不足时会同比缩小，所以当一个元素固定值，另一个为flex:1 时，如果内容过多会压缩空间，需要设置 flex-grow: 0; 则空间不足时也不会缩小

#### flex-basis

`flex-basis`定义了在计算剩余空间之前子元素默认的大小。可以设置为某个长度（e.g. 20%, 5rem, etc.）或者关键字。关键字`auto`意味着子元素会按照其本来的大小显示。关键字`content`意味着根据内容来确定大小——这个关键字到目前没有被很好地支持，所以测试起来比较困难

```css
.item {
  flex-basis: <length> | auto; /* default auto */ | max-content | min-content
}
```

#### flex

flex 是 `flex-grow` 、`flex-shrink`、`flex-basis` 三个属性的缩写。默认是 `0  1  auto`

```css
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

#### align-self

通过设置某个子元素的`align-self`属性，可以覆盖`align-items`所设置的对齐方式。属性值与`align-items`中的意义相同

```css
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
stretch 拉伸 会占满整个父元素的高度
```

> float, clear 和 vertical-align 对flex子元素没有任何影响



#### 使用小节

1. 同时定义 justify-content: center;  align-item: center;  可以让元素水平垂直居中,不确定高度和宽度的情况下
2. 在实际开发中，我们可能在横向2个块 一个固定宽度 一个 flex： 1， 这时如果flex的内容太多的时候，就会将固定宽度的宽度占据，固定宽度会变小，这时因为固定宽度的子元素没有设置 flex ，则使用默认的 flex: 0 1 auto; 可以缩放，需要设置 flex: 0 0 auto; 就可以解决宽度失效的情况