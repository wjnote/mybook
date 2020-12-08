## js 数组去重
js的数组去重是一个常见的需求，这里总结一下多种去重的方式


### 双重循环
双重循环去重是比较容易实现的

```js
  const unique1 = function(arr) {
    const newArray = [];
    let isRepeat;
    for (let i = 0; i < arr.length; i++) {
      isRepeat = false;

      for (let j = 0; j < newArray.length; j++) {
        if (arr[i] === newArray[j]) {
          isRepeat = true;
          break;
        }
      }
      if (!isRepeat) {
        newArray.push(arr[i])
      }
    }
    return newArray;
  }

  const unique2 = function(arr) {
    const newArray = []
    let isRepeat;
    for (let i = 0; i < arr.length; i++) {
      isRepeat = false;
      for (let j = i + 1; j < arr.length; j++) {
        if (arr[i] === arr[j]) {
          isRepeat = true;
          break;
        }
      }
      if (!isRepeat) {
        newArray.push(arr[i])
      }
    }
    return newArray;
  };

  const unique3 = function(arr) {
    if (!arr.length) return;
    const newArray = [];
    for (let i = 0; i < arr.length; i++) {
      for (j = i + 1; j < arr.length; j++) {
        if (arr[i] === arr[j]) {
          j = ++i;
        }
      }
      newArray.push(arr[i])
    }
    return newArray;
  }

  // 上面的三种当时思路是一样的，只是函数的变形，性能最好的是第一种，第二三种数组中的顺序不确定
```


### 利用索引值
如果索引不是第一个索引，说明是重复值

1. 利用`filter()`过滤功能
2. 利用 `indexOf()` 返回的是第一个索引值,或者出现 -1 表示没有该值
3. 只将数组中第一次出现的值返回，之后出现的都过滤掉

```js
  // 相比性能 第二种会优点
  const unique = function(arr) {
    return arr.filter((item, index) => {
      return arr.indexOf(item) === index;
    })
  }

  const unique1 = function(arr) {
    const newArr = []
    arr.forEach(item => {
      if (newArr.indexOf(item) === -1) {
        newArr.push(item)
      }
    })
    return newArr;
  }
```


### 先排序 然后再元素比较
利用数组的原生排序方法，然后在比较每一个元素和后面的一个元素，如果是不同的则添加

> 有一个缺点就是 `sort()` 方法会改变数组的排序顺序，可能不是我们想要的效果

```js
  const unique = function(arr) {
    const newArr = [];
    arr.sort();
    for (let i = 0; i < arr.length; i++) {
      if (arr[i] !== arr[i + 1]) {
        newArr.push(arr[i])
      }
    }
    return newArr
  }
```

### ES6 的新方法 includes

```js
  const unique = function(arr){
    const newArr = [];
    arr.forEach(item=>{
      if(!newArr.includes(item)){
        newArr.push(item)
      }
    })
    return newArr;
  }
  // 需要注意这是ES6方法，IE不兼容
```

### ES6 的 reduce
1. 先数组排序，然后利用累加方法来添加到新数组

> 使用了sort 方法，会将数组的元素顺序改变

```js
  const unique = function(arr){
    return arr.sort().reduce((init, current)=>{
      if(init.length === 0 || init[init.length -1] !== current){
        init.push(current)
      }
    }, [])
  }
```

### Map Set 对象
1. 利用Map Set不会重复的特性去重

> 需要注意Map 和 Set 是ES6的新特性，注意兼容性

```js
  const unique = function(arr) {
    const newArr = [];
    const tmp = new Map()
    for (let i = 0; i < arr.length; i++) {
      if (!tmp.get(arr[i])) {
        tmp.set(arr[i], 1);
        newArr.push(arr[i])
      }
    }
    return newArr
  }

  const unique2 = function(arr) {
    const tmp = new Map();
    return arr.filter(item => {
      return !tmp.has(item) && tmp.set(item, 1)
    })
  }

  const unique1 = function(arr) {
    const set = new Set(arr);
    return Array.from(set)
  }

  // 简易版
  [...new Set(arr)];
```


### 总结
上面的方式如果不考虑兼容，Map是最优的方式，

---

除了考虑时间复杂度和性能之外，还要考虑数组的一些特殊值，才能选择哪种方式最合适
```js
const arr = [1, 1, '1', '1', 0, 0, '0', '0', undefined, undefined, null, null, NaN, NaN, {}, {}, [], [], /a/, /a/];
```

