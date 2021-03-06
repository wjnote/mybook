## 踩坑记录2

1. 在代码中设置HTML的内容一般会用 `val ? val : ''` 的形式加一层判断，但是这种需要注意的是，如果是数字的话需要注意 0 这种情况，或者本身就是 `false` 的情况

2. 转换为字符串：可以使用运算符` + `后紧跟一组空引号` '' ` 快速将数字或布尔值转为字符串

3. 数字字符串转为数字，可以使用 加法运算符 + 或者是 -0 两种方式
    ```js
    let int = '15'
    int = +int;   // 该方法使用有限，有些环境下 + 会作为运算符解析
    // or
    int = int - 0;
    ```

4. 浮点数转为整数， `Math.floor() Math.ceil() Math.round()`3种方法，但是开发中可以使用 `|`（位或运算符） 将浮点数截断为整数
    ```js
    let num= 23.9 | 0;  // 23
    let num1 = -23.9 | 0;  // -23
    // 如果n是正数，则n | 0有效地向下舍入。如果n是负数，它有效地四舍五入。
    // 准确的说，该操作删除小数点后的内容，将浮点数截断为整数。
    // 有一个变通使用就是可以从整数末尾删除任意数量的数字， `num / 10*n | 0`
    ```

5. 函数传值的时候的时候可以使用默认值和解构赋值的简易形式，还可以有条件的构建对象
    ```js
    function pick({id, name, age}){
      return{
        guid: id,
        ...(name && {name}),
        ...(age && {age})
      }
    }
    // 其中的 ｛name｝  是ES6对象的简写方式
    ```

6. 双冒号运算符,是一个新的提案，“函数绑定”（function bind）运算符
    ```js
      foo::bar;
      // 等同于
      bar.bind(foo);

      foo::bar(..arguments)
      // 等同于
      bar.apply(foo, arguments)
    ```

7. js 数字类型使用IEEE754格式来表示整数和浮点数值，所以计算的时候会有意料之外的值

    ```js
    let sum = 62.27 - 33  // 29.270000000000003  在控制台会显示
    let temp = 62.27+3    // 65.27000000000001

    // 可以使用 四舍五入为指定位数的小数，我们开发的时候一般是最后的位，一般采用10位
    let parseNumber = parseFloat(temp.toFixed(10));
    ```

8.  css 的高度自适应问题，我们在一个容器中想让div充满容器的，设置了高度100%,，但是其中有一个子容器的高度超过了父元素的100%，这个时候另外的子元素设置了高度为100%的话，出现滚动条的时候就不会暂满父元素的全部高度，只是刚刚设置的100%的高度，所以这个时候可以给 父元素设置`display：flex;display:-webkit-flex;align-items:stretch;` 然后在子元素的 `height:inherit;`这个时候如果父元素的高度因为某个资源撑高了，其他的子元素也会占满父元素的高度

9. `null 和 undefined` 的区别: `undefined` 是基本数据类型，表示未定义缺少的意思，`null` 是引用数据类型，是对象，表示空对象 `undefined` 是从 null 派生出来的， 所以 `undefined == null // true`

10. `delete` 删除操作符 `delete` 操作符用于从对象中删除属性，上面的 x 是一个局部对象，操作符不影响局部变量，如果删除对象的属性则会返回 undefined
    ```js
    var output = (function(x){
      delete x;
      return x;
    })(0)
    console.log(output) // 0
    var x = {foo:1}
    var resu = (function(x){
      delete x.foo;
      return x.foo;
    })(x)
    console.log(resu)  // undefined
    ```

11. 在项目中加载图片的时候可能会加载失败，这时可以在` <img src='' alt='' onerror="defaultImg()">` 加载设置加载失败函数 ，但是函数必须在页面DOM加载之前声明，如果在文档后申请不会生效
    ```js
    function defaultImg () {  
      var img=event.srcElement;
      img.src="../images/index/toutiao.png";
      img.onerror=null; //控制不要一直跳动
    }
    ```

12. 我们在chorme浏览器打断点的时候可以把变量在 `console` 中打印显示，然后可以使用 `copy(变量名)` 就可以将变量的内容复制到粘贴板中在其他地方复制使用

13. `let` 会产生新的块级作用域，在JavaScript中 `for  if  switch   try/catch/finally` 都会产生块级作用域

14. Chrome 中文界面下默认会将小于 12px 的文本强制按照 12px 显示,可通过加入 CSS 属性 `-webkit-text-size-adjust: none;` 解决。

15. 当一个元素的`visibility`属性被设置成`collapse`值后. chrome中使用`collapse`值和使用`hidden`没有区别。 firefox，opera和IE，使用`collapse`值和使用`display：none`没有什么区别。
    - 其中 `display：none` 不显示元素，在文档布局中不再分配空间（回流+重绘）
    - `visibility：hidden` 隐藏元素，在文档布局中仍保留原来的空间（重绘）