# 文件上传下载

## 文件下载
1. HTML 与文件下载
如果在页面上直接下载某些资源（jpg,txt,pdf等）最方便的方式就是使用 HTML5 的 `download` 属性
```html
<a href="index_logo.gif" download="down show name"></a>
```

```js
// 用来检测浏览器是否支持 downloadz
var isSupportDownload = 'download' in document.createElement('a');
```

缺点：
  1. 上述方式是HTML5的属性，在IE下是不兼容的，如果不考虑IE浏览器的话可以使用
  2. 如果是动态的内容则无法实现(例如我们对页面进行分享，图片是根据内容动态生成)



2. 借助HTML5 Blob 实现文本信息文本下载
我们可以将文本或者JS字符串信息借助Blob转换成二进制，然后作为 `<a>` 元素的 `href` 属性，配合 `download` 属性，实现下载

```js
// content指需要下载的文本或字符串内容，filename指下载到系统中的文件名称。
var funDownLoad = function(content, filename){
  var eleLink = document.createElement('a')
  eleLink.download = filename;
  eleLink.style.display = 'none'
  var blob = new Blob([content]);
  eleLink.href = window.URL.createObjectURL(blob);
  document.body.appendChild(eleLink)
  eleLink.click()
  document.body.removeChild(eleLink)
  window.URL.revokeObjectURL(url);

//  window.URL 里面有两个方法：
// createObjectURL 用 blob 对象来创建一个 object URL(它是一个 DOMString)，我们可以用这个 object URL 来表示某个 blob 对象，这个 object URL 可以用在 href 和 src 之类的属性上。
}
// 触发的方式
button.addEventListener('click', function () {
    funDownload(textarea.value, 'test.html');
});
```


3. 借助Base64 实现任意文件下载
 对于非文本文件，也是可以直接触发JS下载的，如果我们想下载一张图片，可以把图片转换为 base64格式，然后下载

```js
var funDownload = function (domImg, filename) {
    // 创建隐藏的可下载链接
    var eleLink = document.createElement('a');
    eleLink.download = filename;
    eleLink.style.display = 'none';
    // 图片转base64地址
    var canvas = document.createElement('canvas');
    var context = canvas.getContext('2d');
    var width = domImg.naturalWidth;
    var height = domImg.naturalHeight;
    context.drawImage(domImg, 0, 0);
    // 如果是PNG图片，则canvas.toDataURL('image/png')
    eleLink.href = canvas.toDataURL('image/jpeg');
    // 触发点击
    document.body.appendChild(eleLink);
    eleLink.click();
    // 然后移除
    document.body.removeChild(eleLink);
};

```

> 在Chrome浏览器下，模拟点击创建的<a>元素即使不append到页面中，也是可以触发下载的，但是在Firefox浏览器中却不行，因此，上面的funDownload()方法有一个appendChild和removeChild的处理，就是为了兼容Firefox浏览器。download属性从Edge13开始支持，理论上，edge也应该支持直接JS触发的浏览器文件下载，