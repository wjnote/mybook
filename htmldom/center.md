# css常用居中方式

1. 如果是一行文字，则可以简单的使用`line-height`的数值等于块元素的高度即可

2. 如果是有几个元素，我们也可以设置垂直居中，但是需要使用伪元素，如果我们使用三个元素设置为行内块，在设置对齐为 `middle`，会出现下面的效果，居中的高度是根据最高的那个元素来设置的，如果设置一个伪元素并设置`height: 100%` 就可以居中
> css中的 `vertical-align` 该属性定义行内元素的基线相对于该元素所在行的基线的垂直对齐
> 
> css中的 `text-align: center` 可以将子元素内部的行内块左右居中，但是其中的文字也会居中

    ![Alt text](./images/01.jpg)

3.  可以使用计算属性 `calc ` ,但是该属性需要注意兼容性

4.  使用 `absolute + transform` 来设置，（IE9及以上兼容，常用方式）

5.  使用绝对定位的方式
    ```css
    .use-absolute{
        position: relative;
        width:200px;
        height:150px;
        border:1px solid #000;
    }
    .use-absolute div{
        position: absolute;
        width:100px;
        height:50px;
        top:0;
        right:0;
        bottom:0;
        left:0;
        margin:auto;  
        background:#f60;
    }
    ```
