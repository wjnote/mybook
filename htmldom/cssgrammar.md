# css语法

###  css的规则可以分为两种，
1. 一种是 `art-rule` 也就是 at 规则
2. 一种是 `qualified rule` 就是普通规则(我们最常用的规则)


### at 规则，下面列举出了会用过的 @ 规则，一些主要是css3标准的内容
```shell
@charset 用于提示css文件使用的字符编码方式，注意需要使用在页面的最

@import  用于引入一个css文件，除了 @charset 规则不会被引入外，其他所有内容都会被引入

@media  就是media query 规则，能对设备有一些判断

@page  用于分页媒体访问的时候，特别是打印页面的时候，页面是一个特殊的盒模型结构，还可以设置它周围的盒

@key-frames  用于定义动画的关键帧，产生一种数据

@fontface   用于定义字体，icon font技术就是使用这个特性实现的

@namespace  用于跟XML命名空间配合的一个规则 表示内部的css选择器都会带上特定的命名空间

@support  检查环境的特性，与media比较类似
```

### 普通规则就是使用选择器设置元素样式，采用 属性和值得方式
css属性值可能是下面得类型

1. css范围的关键字， initial   unset   inherit 任何属性值
2. 字符串  content属性的值
3. URL： 使用URL()函数的URL值
4. 整数/实数： 比如flex属性
5. 维度： 单位的整数 实数，比如width属性
6. 百分比：大部分维度都支持百分比
7. 颜色，十六进制或者255进制
8. 2D位置： 比如 `background-postion`属性
9. 函数: 来自函数的值，transform 函数的值

#### 我们要重点了解一下css的函数，目前该类属性支持度越来越好

图片类型的
1. imagefunction()
2. filter 函数
3. blur() 函数
4. brightness() 函数
5. contrast()
6. drop-shadow()
7. invert()
8. opacity()
9. saturate()
10. sepia()
11. cross-fade()
12. element()
13. image-set()

图形绘制类型的
1. conic-gradient()
2. linear-gradient()
3. radial-gradient()
4. repeating-linear-gradient()
5. repeating-radial-gradient()
6. shape()
7. toggle() 函数在规则中多余一个元素时生效，会在几个值来回切换

布局类型
1. calc()  函数是基本的表达式计算，可以使用加减乘除，在针对维度计算时，可以允许不同的单位混合(%, px, em, rem)，其中的元素符号前后都最好有一个空格(加减运算必须有)
2. calmp()
3. fit-content()
4. max()
5. min()
6. minmax()
7. repeat()

变形/动画 类型
1. transform
2. matrix()
3. matrix3d()
4. perspective()
5. rotate()
6. rotate3d()
7. rotateX() rotateY()  rotateZ()
8. scale() 
9. scale3d()
10. scaleX()  scaleY()  scaleZ()
11. skew()   skewX()  skewY()
12. translate()
13. translate3d()
14. translateX()   translateY()  translateZ()

环境与元素
1. var()
2. env()
3. attr()



### 伪类选择器
伪类选择器和一般的DOM中的元素不同，并不会改变任何DOM元素，只是插入一些装饰的元素，对用户可见，对DOM不可见，

伪类对元素进行分类是基于特征而不是它的名字，属性或者内容

```css
:focus   伪类将应用于拥有键盘输入焦点的元素

:first-child   伪类向元素的第一个子元素添加样式。

:lang(en)   CSS 伪类基于元素语言来匹配页面元素。 `p:lang(en)` 选取任意的英文段落
<q lang="en">This English quote has a <q>
q:lang(fr){color: #f00;}
```

### 伪元素选择器
伪元素的效果是需要通过添加一个实际的元素才能达到的，常用的有四种伪元素选择器
```css
:first-line  为某个元素的第一行文字使用样式；
:first-letter  为某个元素中的文字的首字母或第一个字使用样式；
:before  在某个元素之前插入一些内容；
:after  在某个元素之后插入一些内容；
```

还有结构性伪类选择器
```css
:root()  选择器表示根元素，在HTML文档就表示html元素   :root{background-color: #999;}
:not()  否定选择器，**目前基本不支持**
:empty()   选择器表示的就是空，没有一点内容的（即便是一个空格）
:target()  选择器来对页面某个target元素(该元素的id被当做页面中的超链接来使用)指定样式，该样式只在用户点击了页面中的超链接，并且跳转到target元素后起作用


first-child、last-child、nth-child、nth-last-child、
nth-child(n)、nth-child(odd)、nth-child(even)、nth-last-child(odd)、nth-last-child(even)
```


### 其中表单元素还有一些特殊的css3选择器
```css
E:checked{}   处于选中状态的元素E的样式
E:enabled{attribute}
E:disabled{}
E::selection{}  E元素中被用户选中或处于高亮状态的部分

input:checked{ width: 50px;}
```