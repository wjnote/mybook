## Retina 下1px的解决方案
1. viewport
可以通过动态的根据设备的dpr来改变页面的 meta来正常显示
```js
const dpr = window.devicePixelRatio;
const meta = document.createElement('meta')
meta.setAttribute('name','viewport')
meta.setAttribute('content', `width=device-width, user-scalable=no, initial-scale=${1/dpr}, maximum-scale=${1/dpr}, minimum-scale=${1/dpr}`) // 动态初始缩放、最大缩放、最小缩放比例
```
> 但是在国内某些手机上的device-width会存在一些误差，导致页面的展示和我们预想的不一致


2. [PostCSS Write SVG](https://github.com/jonathantneal/postcss-write-svg)
我们可以使用postcss插件来完成1px的效果，安装可以查看Github用法（我尝试了一下好像在css文件可以成功，但是在less文件是失败了）
```less
// 可以使用 border-image 来实现1px边框
@svg 1px-border {
    height: 2px;

    @rect {
        fill: var(--color, black);
        width: 100%;
        height: 50%;
    }
}
.example{
  border: 1px solid transparent;
  border-image: svg(1px-border param(--color #000000)) 2 2 stretch;
} 
// 其中的 #000000 就是边框的颜色


// 还可以使用下面的 background-image 的方式来实现
@svg square {
    @rect {
        fill: var(--color, black);
        width: 100%;
        height: 100%;
    }
}

.example {
  background: #000000 svg(square param(--color #fff));
}
// 其中 #000 就是边框颜色 #fff就是这个元素块的背景颜色 但是背景颜色不能设置为 transparent 透明，否则前面的边框颜色就会把整个块元素沾满
```


3. 还可以使用 transform 和伪元素 的配合实现1px的效果(主流浏览器兼容的不错)
```css
.hairlines li{
    position: relative;
    border: none;
}
.hairlines li:after{
    content: '';
    position: absolute;
    left: 0;
    background: #000;
    width:100%;
    height:1px;
    transform: scaleY(0.5);
    transform-origin: 0 0;
}
```

4. 颜色渐变 linear-gradient
通过颜色渐变，也可以实现移动端1px的线，主要是从白色开始渐变这样白的部分就看不到，类始于实现效果
```css
div.linear::after {
    display: block;
    content: '';
    height: 1px;
    background: linear-gradient(0, #fff, #000);
}
```

5. box-shadow 也可以实现1px，实现原理就是将纵坐标设置为0.5px，但是safari不支持小数设置，使用时要慎重
```css
div.shadow{
    box-shadow: 0 0.5px 0 0 #000;
}
```