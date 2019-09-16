# moment常见使用
Moment.js是一个轻量级的JS时间库，主要是对时间的开发，记录一下以便日常的开发。

日常使用中主要涉及的时间操作，获取时间，设置时间，格式化时间，两个时间的比较

### 格式化时间,开发中最常用的功能
```js
  moment().format()
  moment().format(String)
```
1. 格式化年月日
    ```js
      moment().format('YYYY-MM-DD HH-mm-ss')
      moment().format('YYYY年MM月DD日')
    ```
2. 格式化时分秒
    ```js
      moment().format('HH时mm分ss秒')  // 24小时进制
      moment().format('hh:mm:ss a')   // 12小时进制
    ```
3. 格式化为时间戳(可用于比较2个时间)
    ```js
      moment().format('X') // 返回值为字符串类型 以秒为单位
      moment().format('x') // 返回值为字符串类型 以毫秒为单位
    ```

### 获取时间,获取的是一个moment对象，可以使用 `.toObject()` 来转换为对象的形式

1. 获取具体时间()
    ```js
      moment().startOf(String) // 执行moment函数返回一个moment时间对象

      moment().startOf('day')   // 获取今天0时0分0秒
      moment().startOf('week')  // 获取本周第一天(周日)0时0分0秒
      moment().startOf('isoWeek')  // 获取本周周一0时0分0秒
      moment().startOf('month')  // 获取当前月第一天0时0分0秒
    ```
    ```js
      moment().endOf(String)  // 获取结束的时间

      moment().endOf('day')   // 获取今天23时59分59秒
      moment().endOf('week')  // 获取本周最后一天(周六)23时59分59秒
    ```

2. `moment().daysInMonth()` 获取当月的总天数

3. 获取时间戳(以毫秒为单位)，可以使用于对时间插件的不可选择日期来设置
    ```js
      moment().format('x') // 返回值为字符串类型
      moment().valueOf() // 返回值为数值型
    ```

4. 获取某一个单独的信息
    ```js
      // 获取年份
      moment().year()
      moment().get('year')

      // 获取月份
      moment().month() (0~11, 0: January, 11: December)
      moment().get('month')

      // 获取某一天 两种方式都是可以的
      moment().get('day')
      moment().get('weekday')
      moment().get('isoWeekday')

      moment().get('hours')
      moment().get('minutes')
      moment().get('seconds')

      // 当前的年月日时分秒
      moment().toArray()      // 以一个数组的形式来返回
      moment().toObject()     // 以一个对象的形式来返回
    ```


### 设置时间，使用设置时间的方法后也是返回一个 `moment` 对象

```js
   // 两种方式都是可以的1
   moment().set('year', 2019)
   moment().set({year: 2019})
   moment().set('month', 11)  // 需要注意月份的默认数值是  0 - 11
   moment().set('date', 15)
   moment().set('weekday', 0)  // 设置日期为本周第一天（周日）
   moment().hours(12)
```
1. 添加时间，默认是在当前的基础之上添加时间
    ```js
      moment().add(Number, String)
      moment().add(Object)

      moment().add({years: 1})  //  case
    ```
2. 减少时间，默认是在当前的基础之上减少时间
    ```js
      moment().subtract(Number, String)
      moment().subtract(Object)
    ```

### 比较时间
` moment().diff(Moment|String|Number|Date|Array) `

1. 获取2个日期之间的时间差
    ```js
      let start_date = moment().subtract(1, 'weeks')
      let end_date = moment()

      end_date.diff(start_date) // 返回毫秒数

      end_date.diff(start_date, 'months') // 0
      end_date.diff(start_date, 'weeks') // 1
      end_date.diff(start_date, 'days') // 7
      start_date.diff(end_date, 'days') // -7
    ```

### 转化为JavaScript原生Date对象
` moment().toDate()  |  new Date(moment()) `

### 将moment时间转化为JS原生Date对象
```js
let m = moment()
let nativeDate1 = m.toDate()
let nativeDate2 = new Date(m)

String(nativeDate1) === String(nativeDate2) // true
```

**开发中常用的一些实战**

- 获取昨日0时0分0秒到昨日23时59分59秒， 格式：[milliseconds, milliseconds]
- 获取上周一到上周日时间范围，格式: [seconds, seconds]
- 获取上个月第一天和最后一天时间范围， 格式：[YYYY-MM-DD, YYYY-MM-DD]