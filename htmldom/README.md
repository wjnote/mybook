## html/DOM
工作以后渐渐都是在使用jQuery，有时候原生的JavaScript操作会变得模糊不清，再次总结一下常用的原生DOM API。
> 有些是HTML5的新标准，使用时需要查询一下[can i use](https://caniuse.com/#)。 主要是针对IE的兼容。


DOM 就是浏览器为JavaScript提供的一系列接口（通过window.document提供）使我们可以操作DOM页面，DOM是文档模型该模型是独立于语言的，所有其他语言也可以操作，比如 Python...等

#### DOM创建
DOM节点通常对应于一个标签，一个文本或者是一个HTML属性，DOM有一个 nodeType 来表示当前元素的类型
1. Element, 元素
2. Attribute， 属性
3. Text， 文本

```js
let ele = document.createElement('div')
// 下面是一个文本节点 它的nodeType : 3
let docNode = document.createTextNode('hello world')

ele.appendChild(docNode);

<div>hello world</div> // 在页面显示的最终结果
```

#### DOM查询
```js
let ele = document.getElementById('xxx')
let eles = document.getElementByClassName('classname')
let eles = document.getElementByName('elename')

// 新增了查询具体的方式
let newEle = document.querySelector('xxx') // 其中用id或class都可以
let newEles = document.querySelectorAll('div.note') // 基本上实现了jQuery选择器

let childnoe = parent.querySelector('xxx')
```

还可以通过一个节点查找父节点，子节点，兄弟节点
```js
// 获取父元素，  父节点
let parent = ele.parentElement;
let parent = ele.parentNode;

// 获取子元素，可以是任意的元素 可以通过 nodeType 来判断
let nodes = ele.children;

// 当前元素的第一个子元素 、 最后一个子元素
let el = ele.firstElementChild;
let el = ele.lastElementChild;

// 查询兄弟节点
let el = ele.nextElementSibling;
let el = ele.previousElementSibling;
```
> 注意其中使用的 firstElementChild 而不是 firstChild 因为在现代浏览器中，我们编写的空格会被解析成一个文本节点，firstElementChild 查询的是第一个子元素节点，更加准确

#### DOM更改
```js
// 添加删除子元素
ele.appendChild(el)
ele.removeChild(el)

// 替换
ele.replaceChild(el1, el2)
// 插入子元素
parentElement.insertBefore(newElement, referenElement)

// 清空子节点，没有原生的方法，可以用下面来实现
var element = document.getElementById("app");
while (element.firstElementChild) {
  element.removeChild(element.firstElementChild);
}
```
> 在某一个元素之后插入元素需要自己实现，js原生没有，可以参考jQuery的 after方法，一个简单实现 `parentDiv.insertBefore(sp1, sp2.nextSibling);`


#### 属性操作
```js
// 获取的是一个 对象形式的数组  取具体的值需要使用 attrs[name].value
let attrs = ele.attributes; // 该属性是只读的

// 获取，设置属性
let c = el.getAttribute('class')
ele.setAttribute('class', 'value')
// 判断 移除属性 ,还有其他已经废弃
ele.hasAttribute('class')
ele.removeAttribute('class')
```

#### class操作
元素的class操作是一个常规的操作，
```js
let classLists = ele.classList;   // 该属性是只读的，但是可以通过下面的方法修改

classLists.add(String [, String]) // 添加指定的类值，如果已经存在则忽略
classLists.remove(String [, String]) // 删除指定的类值
classLists.item(Number) // 按集合中的索引返回类值，所有从0开始
classLists.toggle(String [, force]) // 只有一个参数，表示切换指定的类值，如果存在第二个参数，第二个参数的计算结果为true则添加，否则删除
classLists.contains(String) // 检查元素中是否存在指定的值
classLists.replace(oldClass, newClass) // 用新类替换已有类
```
> ie9 不支持，IE10不支持方法操作，

#### 直接访问元素样式，是元素的属性，而不是方法访问
```js
var myDiv = document.getElementById("myDiv");
//设置背景颜色
myDiv.style.backgroundColor = "red";
//改变大小
myDiv.style.width = "100px";
myDiv.style.height = "200px";
//指定边框
myDiv.style.border = "1px solid black";
```

#### 事件绑定
目前主要使用的是DOM2级的事件绑定事件，在`addEventListener` 中有一个事件参数 `event`,里面保存了触发事件相关的内容
- `event.target`: 指向触发事件监听的对象。
- `event.currentTarget`: 指向添加监听事件的对象, 一般等于在方法中的`this`,在方法中一般不建议使用 this
```html
<ul>
  <li>hello 1</li>
  <li>hello 2</li>
  <li>hello 3</li>
  <li>hello 4</li>
</ul>
<script>
  let ul = document.querySelectorAll('ul')[0]
  let aLi = document.querySelectorAll('li')
  ul.addEventListener('click',function(e){
      let oLi1 = e.target
      let oLi2 = e.currentTarget
      console.log(oLi1)   //  被点击的li
      console.log(oLi2)   // ul
      console.log(oLi1===oLi2)  // false
      console.log(this===oLi2)  // true
  })
</script>
```