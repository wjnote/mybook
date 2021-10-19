## Retina 下1px的解决方案
当逻辑像素是 1px 时，在 DPR 为 2 的 设备上显示为 2px 的物理像素, 在 DPR 为 3 的 设备上显示为 3px 的物理像素

1. 利用CSS的伪元素 `::after + transform `进行缩放，因为伪元素 `::after  ::before` 是独立与当前元素的，可以对其单独缩放而不影响元素本身，下面的这种方式只是针对 `dpr=2`的高清屏,**这种方式也是一些开源的库使用的方式**

```html
  <div class="cell border1px"></div>

  <style>
    .cell{width:100px; height:100px; position: relative;}
    /* 元素的四边边框都存在 */
    .border1px::after{
      content: ' ';
      position: absolute;
      top: 0; left: 0;
      box-sizing: border-box;
      width: 200%;
      height: 200%;
      border: 1px solid  #f00;
      border-radius: 4px;
      transform: scale(0.5);
      -webkit-transform-origin: top left;
    }
    /* 单边框 以上边框为例 */
    .border1px-top::after{
      content: ' ';
      position: absolute;
      left : 0;top:0;right:0;
      border-top: 1px solid red;
      transform: scaleY(0.5);
      transform-origin: left top;
    }
  </style>
```


2. 使用less对上面的代码封装，同时增加不同的 DPR 设备的查询，设置不同的缩放
```less
.border(
  @borderWidth: 1px;
  @borderStyle: solid;
  @borderColor: @lignt-gray-color;
  @borderRadius: 0){
    position: relative;
    &::before{
      content: '';
      position:absolute;
      width: 98%;
      height: 98%;
      top:0; left:0;
      -webkit-transform-origin: left top;
      transform-origin: left top;
      box-sizing: border-box;
      pointer-events: none;
    }
    @media(-webkit-min-device-pixel-ratio: 2){
      &::before{
        width: 200%;
        height: 200%;
        -webkit-transform: scale(0.5)
      }
    }
    @media(-webkit-min-device-pixel-ratio: 2.5){
      &::before{
        width: 275%;
        height: 275%;
        -webkit-transform: scale(1 / 2.75)
      }
    }
    @media(-webkit-min-device-pixel-ratio: 3){
      &::before{
        width: 300%;
        height: 300%;
        transform: scale(1 / 3);
        -webkit-transform: scale(1 / 3)
      }
    }
    .border-radius(@borderRadius);
    &::before{
      border-width: @borderWidth;
      border-style: @borderStyle;
      border-color: @borderColor
    }
  }
.border-all(
  @borderWidth: 1px; 
  @borderStyle: solid; 
  @borderColor: @lignt-gray-color; 
  @borderRadius: 0) {
    .border(@borderWidth; @borderStyle; @borderColor; @borderRadius);
}
```



### 出了上面两种常用的方式还有其他方案

1. 使用图片来模拟1px边框，兼容性最好，灵活性最差，不能改变颜色，长度

2. `viewport` JS可以通过动态的根据设备的 dpr 来改变页面的 meta来正常显示，但是这种只使用一开始就这样设置，会导致页面所有的像素都改变，此时页面的宽度为`750px: iphone6`

   ```js
   const dpr = window.devicePixelRatio;
   const meta = document.createElement('meta')
   meta.setAttribute('name','viewport')
   // 动态初始缩放、最大缩放、最小缩放比例
   meta.setAttribute('content', `width=device-width, user-scalable=no, initial-scale=${1/dpr}, maximum-scale=${1/dpr}, minimum-scale=${1/dpr}`) 
   ```

   >  但是在国内某些手机上的 device-width 会存在一些误差，导致页面的展示和我们预想的不一致
3. [PostCSS Write SVG](https://github.com/jonathantneal/postcss-write-svg)
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


4. 颜色渐变 `linear-gradient` 通过颜色渐变，也可以实现移动端1px的线，主要是从白色开始渐变这样白的部分就看不到，类始于实现效果
```css
div.linear::after {
    display: block;
    content: '';
    height: 1px;
    background: linear-gradient(0, #fff, #000);
}
```

5. `box-shadow` 也可以实现1px，实现原理就是将纵坐标设置为0.5px，但是safari不支持小数设置，使用时要慎重
```css
div.shadow{
    box-shadow: 0 0.5px 0 0 #000;
}
```