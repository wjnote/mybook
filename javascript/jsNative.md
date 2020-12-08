## 原生js汇总

JS 原生获取DOM的方式比jQuery类的插件快很多，尽量使用原生的方式

```js
document.getElementById('id')
document.querySelector('#id')
document.querySelectorAll('.class')
document.getElementsByClassName('myclass')[0]  // 通过class获取元素
```

> js修改css样式

 `document.getElementById('myid').style.display = 'none';`

> class操作  元素有一个 classList 属性可以获取属性名列表
```js
document.getElementById('myid').className = 'active div-1';  // 设置class名字
document.getElementById('myid').className = '';  // 清空class名字

document.getElementById('myid').classList.item(0);//item为类名的索引
document.getElementById('myid').classList.add('newClass');// 添加class 返回undefined
document.getElementById('myid').classList.remove('newClass');//移除class 返回undefined
document.getElementById('myid').classList.toggle('newClass');//切换，有则移除，没有则添加
document.getElementById('myid').classList.contains('newClass');//判断是否存在该class
```

> js创建元素 并添加文本
```js
var newdiv = document.createElement('div')
var newText = document.createTextNode('this is content value')
newdiv.appendChild(newtext);
document.body.appendChild(newdiv);

// removeChild()移除节点，并返回节点
// cloneNode()复制节点
// insertBefore()插入节点（父节点内容的最前面）
// insertBefore()有两个参数，第一个是插入的节点，第二个是插入的位置
```

> js返回所有子节点对象 childNodes
```js
var mylist = document.getElementById('myid');
for(var i=0,i<mylist.childNodes.length;i++){
console.log(mylist.childNodes[i]);
}
// firstChild返回第一个子节点
// lastChild返回最后一个子节点
// parentNode返回父节点对象
// nextSibling返回下一个兄弟节点对象
// previousSibling返回前一个兄弟节点对象
// nodeName返回节点的HTML标记名称
```