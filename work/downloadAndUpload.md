# 文件上传下载

## 文件下载
1. HTML 与文件下载
    如果在页面上直接下载某些资源（jpg,txt,pdf等）最方便的方式就是使用 HTML5 的 `download` 属性

  ```js
  // 用来检测浏览器是否支持 downloadz
  var isSupportDownload = 'download' in document.createElement('a');
  ```

该方法的缺点：
  1. 上述方式是HTML5的属性，在IE下是不兼容的，如果不考虑IE浏览器的话可以使用
  2. 如果是动态的内容则无法实现(例如我们对页面进行分享，图片是根据内容动态生成)



2. 借助HTML5 Blob 实现文本信息文本下载 [Blob对象](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/Blob)
    我们可以将文本或者JS字符串信息借助Blob转换成二进制，然后作为 `<a>` 元素的 `href` 属性，配合 `download` 属性，实现下载

    > 请求接口的数据格式 responseType: arraybuffer  或者是 blob 格式

    ```js
    axios.get(`url`, {
            responseType: 'arraybuffer',
        }).then(res => {
            if (res.status == 200) {
                let blob = new Blob([res.data], {
              		 // 需要设置 文档格式，否则下载的就是 txt 文件，并乱码    
                    type: 'application/vnd.ms-excel;charset=utf-8'
                });
                let fileName = `name`;
                // for IE
                if (window.navigator && window.navigator.msSaveOrOpenBlob) {
                    window.navigator.msSaveOrOpenBlob(blob, fileName);
                } else {
                    // for Non-IE
                    let objectUrl = URL.createObjectURL(blob);
                    let link = document.createElement("a");
                    link.href = objectUrl;
                    link.setAttribute("download", fileName);
                    document.body.appendChild(link);
                    link.click();
                    window.URL.revokeObjectURL(link.href);
                }
            } else {
                // error handler
            }
        });
    ```

    

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



## 文件上传
一般的用户选择了要上传的文件后，input标签在每次用户选择文件之后都会触发onchange事件，通过访问input的files属性，就可以获取到用户选择的文件集合，这个对象里面包含了name(文件名), size(文件大小), type(文件种类)等等，然后就是传递给后台，使用form的方式

> form 默认使用的是`enctype: application/x-www-form-urlencoded`，如果是包含了文件上传控件或者是传递大文件的话需要设置 form 的 `enctype="multipart/form-data"`

前端做文件上传的时候，需要考虑文件类型以及如何处理文件类型的数据
    - 内置文件流接口： Blob 文件流接口定义
        - 内置文件流对象： File (单文件，继承于接口Blob，可以使用Blob的方法) 和 FileList(多文件集合)
        - 内置文件流读取对象： FileReader (单文件读取)


### 项目中使用 FormData 来上传
`FormData` 类型其实是在 `XMLHttpRequest` 2级定义的，它是为序列化以及创建表格格式相同的数据，`FormData`对象用以将数据编译为键值对以便用`XMLHttpRequest`来传输。

如果表单 `enctype` 属性设置为`multipart/form-data`,则会使用表单的`submit()`方法来发送数据

需要使用input来上传的话需要设置 `<input class="" type="file" accept="undefined" name="file" multiple="" />`，其中的 multiple设置后就可以使用formdata来上传，当点击上传成功后，刚刚上传的文件内容就保存在`input`标签的`files`属性上，数组的方式保存

1. 创建一个空对象实例  `let formData = new FormData();`
2. 也可以使用页面上的表格来初始化 `formData` 对象 

```js
let formData = new FormData();
formData.append('name', blobData);
//  上传图片，问价都是流的形式，需要设置ajax不处理数据，也不设置内容类型
$.ajax({
  url: '',
  type: 'POST',
  data: formData,
  cache: false,
  async: false,
  processData: false,  // 不处理数据
  contentType: false,  // 不设置内容类型
  success: function() {}
});
```

```js
var formData = new FormData();

formData.append("username", "Groucho");
formData.append("accountnum", 123456); //数字123456会被立即转换成字符串 "123456"

// HTML 文件类型input，由用户选择 文件是保存在 XXX.files属性上的
formData.append("userfile", fileInputElement.files[0]);

// JavaScript file-like 对象
var content = '<a id="a"><b id="b">hey!</b></a>'; // 新文件的正文...
var blob = new Blob([content], { type: "text/xml"});

formData.append("webmasterfile", blob);

var request = new XMLHttpRequest();
request.open("POST", "http://foo.com/submitform.php");
request.send(formData);
```
字段 `userfile` 和 `webmasterfile`  都包含一个文件. 字段 "accountnum" 是数字类型，它将被FormData.append()方法转换成字符串类型(FormData 对象的字段类型可以是 Blob, File, 或者 string: 如果它的字段类型不是Blob也不是File，则会被转换成字符串类)。