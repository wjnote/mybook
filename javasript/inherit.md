## 函数继承

### 组合继承的方式
```js
function Parent(value){
    this.value = value
}
Parent.prototype.getValue =function(){
    console.log(this.value)
}
function Child(value){
    Parent.call(this,value)
}
Child.prototype = new Parent();
```
组合继承的核心在于子类构造过程中，使用`Parent.call(this)`来继承父类的属性，然后改变子类的原型为父类的实例来继承父类的函数
优点：
1. 可以传参给父类的构造函数，不会引起父类引用属性共享，可以复用父类的函数
缺点：
1. 在继承父类函数的时候会调用父类的构造函数，导致子类的原型上多了很多不需要的父类的属性，存在内存上浪费。


### 寄生组合继承
上面的组合继承由于继承了父类不需要的属性的缺点，这种继承对组合继承进行了优化，组合继承的缺点在于继承父类的时候调用了父类的构造函数，我们优化掉就好了；**将父类的原型赋值给子类，并且将构造函数设置为子类，就解决了无用的父类属性，还能正确找到子类的构造函数**
```js
function Parent(value){
    this.value = value;
}
Parent.prototype.getValue =function(){
    console.log(this.value)
}
function Child(value){
    Parent.call(this,value)
}
Child.prototype = Object.create(Parent.prototype, {
    constructor: {
        value: Child,
        enumerable: false,
        writable: true,
        configurable: true
    }
})
// 也可以使用下面的写法 但是上面的写法更加接近默认行为，修改了enumerable为false，这样遍历的时候就不会遍历到该属性
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
// 添加别的函数的时候只能使用 `Child.prototype.Fnname = function(){} ` 的方式单个添加
```

### class继承
在js中不存在类，class只是函数的语法糖，class继承的核心在于 extends 表明继承自那个类，并且在子类中必须调用super，这个就可以看作是`Parent.call(this, value)`
```js
class Parent{
    constructor(value){
        this.value = value
    }
}
Parent instanceof Function // true  可以看出class就是函数

class Child extends Parent {
    constructor(value){
        super(value);
        this.val = value;
    }
}
```