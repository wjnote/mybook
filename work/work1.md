## 踩坑记录1

> 开发者常常忽略的问题，每个文件写15条内容

1. 判断一个数组中有一个特定的值，可以使用find函数，但是IE中需要使用 `indexOf` 来判断返回值和 `-1` 的关系，还可以使用 `~index`的方式来判断
    ```js
      let arr = ['geag', 1, 'geagr']
      let temp = 'geag'
      let index = arr.indexOf(temp)
      if (index > -1) {
        console.log(111, '其中有该值')
      }
      if (~index) {	
        console.log(222, '其中有该值')
      }
      // 只有 ~-1 = 0 不会进入函数，其他的任何值都会进入函数
    ```

2. 目前政府的项目中使用`iframe`的方式， `iframe `的通信中，父子通信的方法和属性只能存在`html 页面script`标签内，如果是存在 `js` 文件中的方法和属性是访问不到的，
    ```html
      <iframe name="myiframe" src="./iframe1.html" frameborder="0" class="myiframe"></iframe>
      iframeName.window.document.XXX // 调用子iframe的方法，
      parent.window.XXX  // 调用父iframe的方法
    ```

3. 在 JS 文件中有时候使用开关的方式来作为判定条件，设置默认值的时候最好设置为 `false` ，如果设置为`true`的话，当有值且值为 `false `的时候，在 `||` 运算的时候会默认取后面的值，这样就不能达到想要的效果

4. 页面中为一个元素绑定事件，事件执行后,页面未刷新且元素还在，然后你再次点击，元素又被绑定个一次点击事件，这样第二次点击就会执行两次，以此类推。如何解决累加绑定 ： 元素是已经存在页面上的则可以使用 off 取消绑定的事件。  如果是动态添加到页面上的元素呢 ：可以使用事件代理的方式代理到父元素上

5. ES6在不同的浏览器有不同的表现，例如对象的属性简写在IE中不能实现，对象的`key`值为对象时，在谷歌中算一个属性(会调用对象的`toString()`方法)，而在IE中不算属性会忽略

6. 对象字面量的方式来获取不同类型的变量（IE9也可以实现）
    ```js
      const temp = 'dev';
      const temptest = 'test';
      const status = {
        'dev': 'this is dev',
        'prod': 'this is prod'
      }
      console.log(status[temp])  // 'this is dev'
      // 可以修改 temp的值来改变 status的结果值，这种在条件判断是可以适用的
    ```

7. 函数自执行的几种方式
    ```js
    const Func = function(){}();  // 赋值给一个变量常用的

    (function(){})()
    (function(){}())

    // 上面两种都是常用的， 还有可能会看到下面四种方式 主要是一些兼容的方式，防止压缩时报错
    + function(){}();
    - function(){}();
    ! function(){}();
    ~ function(){}();
    ```

8. 开发中函数内部有时候有很多比较复杂的分支，我们可以使用惰性载入函数，只是在第一次进入的时候进行判断，可以节省资源开支，形式是会返回一个替代函数
    ```js
    function Func() {
      let a = 10;
      let b = 12;
      if (a === b) {
        Func = function() {
          console.log(`x`)
        }
      } else {
        Func = function() {
          console.log(`Y`)
        }
      }
      return Func();
    }
    ```

9. 过滤数组中的`false`值，比如`0, false , null, undefined, NaN`等可以使用简易方法
    ```js
    const arr = [0,1,'0','1',undefined, null, '1'+0];
    arr.filter(Boolean);
    // [1,'0','1','10'];
    ```
> 其中 Boolean 是一个函数，会判断传入的值返回 true 、 false；

10. 过滤数组的重复的值,利用了`indexOf`返回的是第一次出现的位置
    ```js
    arr.filter((key,index)=>arr.indexOf(key) === index)
    ```

11. 利用扩展运算符，将多维数组变为一维数组
    ```js
    //  如果只是二维数组
    let arr = [1, [2,3,4], 5, [6,7]];
    [].concat(...arr) // [1,2,3,4,5,6,7]
    // 如果是多维数组 可以利用递归调用的方式, 一次一次的降维 直到一维数组
    function flattenArray(arr) {
      const flattened = [].concat(...arr);
      return flattened.some(item => Array.isArray(item)) ? flattenArray(flattened) : flattened;
    }
    ```

12. 利用`...`运算符来产生根据条件的不同的对象，下面的方式，不用`...`会报格式错误 ？？
    ```js
    const getUser = (temp)=>{
      return {
        name: 'wuj',
        ...temp && {age: 26}
      }
    }
    //  结果就是传入为true时，返回两个属性
    ```

13. 在项目中使用select的时候一般是需要去除他本来的样式
    ```css
    select {
        /*Chrome和Firefox里面的边框是不一样的，所以复写了一下*/
        border: solid 1px #000;
        /*很关键：将默认的select选择框样式清除*/
        appearance:none;
        -moz-appearance:none;
        -webkit-appearance:none;
    }
    /*清除ie的默认选择框样式清除，隐藏下拉箭头*/
    select::-ms-expand { display: none; }
    ```

14. css的两端对齐，常常使用 `text-align: justify`的方式，但是`justify`对最后一行无效, 如果我们想整个文段两端对齐的话，可以使用`text-align-last: justify`,但是该属性的方式需要兼容性，我们还可以新增一行，使该行文本不是最后一行。
  
    **使用它实现两端对齐，需要注意在元素之间添加空白符(包括空格、换行符、制表符)才起作用，如果只有一行的话，需要添加两个样式，否则会认为这行是最后一行**

    > 一般的左右格式的文本，左边需要两端对齐的话，如果有 ： 号的话，最好使用 after 伪类的形式 可以实现字数不同的几个字两端对齐
   
    ```css
    .justify{
      text-align: justify;
      text-align-last: justify;
      -moz-text-align-last: justify;
    }
    .css{
      text-align: justify;
      &::after {
          display: inline-block;
          width: 100%;
          content: '';
          height: 0;
      }
    }
    ```

  > 有时候我们给元素设置CSS属性不能达到我们想要的效果，我们可以将 .justify  设置到该元素的父元素上去

15. png / jpg / gif / webp

    - png 是便携式网络图片，是一种无损数据压缩位图文件格式，优点是压缩比高，色彩好，大部分都可以使用
    - jpg 是一种针对相片使用的一种失真压缩方式，是一种破坏性的压缩，在色调和颜色的平滑变化上做的不错，在网络上用来存储和传输照片的格式
    - git 是一种位图文件格式，以8位色重现真色彩的图像，可以实现动画效果
    - webp 是谷歌在2010年推出的图片格式，压缩了只有jpg的 2/3 ,大小比PNG小45% ，缺点是压缩时间长，兼容性不好
