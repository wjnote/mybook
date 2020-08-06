## 粘性页脚
粘性页脚是一种常用的页面布局： 就是页脚始终在页面底部，当页面内容没充满屏幕时页脚固定在底部，当页面内容高于屏幕时，页脚会在内容的底部。

> 实现的方式也是多种多样的, 如果是PC端实现主要采用前面两种，如果是移动端则可以采用css3的新属性

1. 利用绝对定位和 padding 完美实现（需要知道底部的高度）
    1. 给元素设置重置样式，body ,html 设置高度100%
    2. 外部容器设置 min-height:100% 内容较少也可以撑开
    3. 主体内容设置 padding-bottom,值为底部的高度
    4. footer高度固定，进行绝对定位在底部
    5. 兼容性比较高

2. 利用`padding-bottom`和`margin-top`完美兼容（需要知道底部的高度）
    1. 给元素设置重置样式，body ,html 设置高度100%
    2. 外部容器设置 `min-height:100%` 使内容少也可以撑开
    3. 主体内容设置 `padding-bottom` 值为底部的高度
    4. footer高度固定，`margin-top` 值为高度负值, 注意`footer`元素的层级
    5. 兼容性比较高

   ```html
   <div class="container">
     <div class="header"></div>
     <div class="section"></div>
   </div>
   <div class="footer">Copyright© 1994-2019 切图妞</div>
   ```

   ```css
   html,
   body {
     height: 100%;
   }
   .container {
     min-height: 100%;
     height: auto !important;
     height: 100%; /*IE6不识别min-height*/
   }
   .section {
     padding-bottom: 60px;
   }
   .footer {
     position: relative;
     margin-top: -60px;
     width: 100%;
     height: 60px;
   }
   ```

3. flex弹性布局，适用于底部不固定高度，利用flex弹性布局实现效果，兼容有限，主要是移动端使用
     1. 外部容器设置为flex布局容器，`flex-flow: column`并最小高度为100vh
     2. 主体内容flex属性设置 
4. calc计算（IE10不完全兼容），vh 存在兼容有限，一般使用在移动端，`100vh`可以替代body的`100%`来实现视口高度的效果
     1. 外部容器使用calc计算，100vh减去底部高度
     2. `footer`位置与`container`同级，高度固定
     3. 注意内容display属性设置为 `table-row` 高度设置为100%

   ```html
   <div class="container">
     <div class="header"></div>
     <div class="section">
     </div>
   </div>
    <div class="footer">Copyright© 1994-2019</div>
   ```

   ```css
   .container {
     min-height: calc(100vh - 60px);
   }
   .footer {
     height: 60px;
   }
   ```
5. Grid 网格布局（IE10不完全兼容）
    底部不定高度，利用grid网格实现效果，兼容性有限建议移动端使用

    1. 外部容器display设为grid网格布局，grid-template-rows设置一个网格的行，fr单位可以帮助我们创建一个弹列的网格轨道,它代表了网格容器中可用的空间（就像Flexbox中无单位的值）
    2. header头部的位置放在主体内容内部
    3. footer中grid-row-start和grid-row-end属性设置单元格开始和结束的行线

   ```html
   <div class="container">
     <div class="header"></div>
     <div class="section"></div>
     <div class="footer">Copyright© 1994-2019 切图妞</div>
   </div>
   ```
   ```css
   .container {
     min-height: 100vh;
     display: grid;
     grid-template-rows: 1fr auto;
   }
   .footer {
     grid-row-start: 2;
     grid-row-end: 3;
   }
   ```





