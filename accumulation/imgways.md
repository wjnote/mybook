## 图片三种表现方式
图片的表现形式是可以相互转换的,目前在后端存储图片主要的方式(常用第一种)
1. 将图片独立部署在服务器的一个文件夹，然后数据库里面存储图片的路径
2. 将图片变为二进制直接存储在数据库，但是这种方式图片较大的时候会拖慢数据库访问速度

如果是第一种的话，我们直接将后台返回的路径赋值给img的src，但是实际开发中有时候一些第三方的接口只能接收base64格式的图片，就需要相互转换
> 我们使用 axios 请求图片资源时，axios会默认将二进制文件变为json文本形式会产生乱码，可以设置axios返回blob的文件格式，然后前端用一个blob对象来接收显示图片。或者转变为base64的格式显示



![图片转换形式](./img/imgways.png)

1. url转换为 base64
```js
const urlToBase64 = function(url){
  return new Promise((resolve,reject) => {
    let image = new Image();
    image.onload = function(){
      let canvas = document.createElement('canvas');
      canvas.width = this.naturalWidth;
      canvas.heigth = this.naturalHeight;
      canvas.getContext('2d').drawImage(image, 0, 0);
      // result
      let result = canvas.toDataURL('image/png')
      resolve(result)
    }
    // cors 策略 会存在跨域问题
    // https://stackoverflow.com/questions/20424279/canvas-todataurl-securityerror
    image.setAttribute('crossOrigin', 'Anonymous');
    image.src = url;
    image.onerror = () => {
      reject(new Error('图片流异常'))
    }
  })
}

// 调用方式
let imgUrl = 'http://xxx.jpg'
const imgBase64 = urlToBase64(imgUrl).then((res) => {
  // 这个res直接是图片的路径 赋值到img的src就可以显示
  console.log(`base64 img ${res}`)
})
// 图片使用base64的路径
<img src=`data:image/jpeg;base64,${base64}`>
```

2. base64 转 blob
```js
// 利用 URL.createObjectURL 为 blob 对象创建临时 URL
function base64ToBlob({ b64data = '', contentType = '', sliceSize = 512 } = {}) {
  return new Promise((resolve, reject) => {
    // 使用 atob() 方法将数据解码
    let byteCharacters = atob(b64data);
    let byteArrays = [];
    for (let offset = 0; offset < byteCharacters.length; offset += sliceSize) {
      let slice = byteCharacters.slice(offset, offset + sliceSize);
      let byteNumbers = [];
      for (let i = 0; i < slice.length; i++) {
        byteNumbers.push(slice.charCodeAt(i));
      }
      // 8 位无符号整数值的类型化数组。内容将初始化为 0。
      // 如果无法分配请求数目的字节，则将引发异常。
      byteArrays.push(new Uint8Array(byteNumbers));
    }
    let result = new Blob(byteArrays, {
      type: contentType
    })
    result = Object.assign(result, {
      // jartto: 这里一定要处理一下 URL.createObjectURL
      preview: URL.createObjectURL(result),
      name: `图片示例.png`
    });
    resolve(result)
  })
}

// 调用方式
let base64 = base64.split(',')[1]

base64ToBlob({b64data: base64, contentType: 'image/png'}).then(res => {
    // 转后后的blob对象
    console.log('blob', res)
})
```

3. blob 转变为 base64 格式
```js
// 原理：利用fileReader的readAsDataURL，将blob转为base64
function blobToBase64(blob) {
  return new Promise((resolve, reject) => {
    const fileReader = new FileReader();
    fileReader.onload = (e) => {
      resolve(e.target.result);
    };
    // readAsDataURL
    fileReader.readAsDataURL(blob);
    fileReader.onerror = () => {
      reject(new Error('文件流异常'));
    };
  });
}
```