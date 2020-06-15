# layui使用记录

> 插件使用，需要将 layui 的所有文件全部放在一处，里面有css引用其他的css文件，而不能只使用 layui.css的文件

## 时间插件的使用
按照官网的使用流程，如果页面有多个时间输入框 `laydate`, 则需要设置 `lay-key="1"` 为不同的值，如果值为一样的，则会导致页面点击时弹出的时间选择关闭



#### layui 如果是2个不同的时间输入框的话，可以设置后面的时间必须大于前面的时间
```js
// 实现的效果就是 先选择开始时间，然后选择结束时间的时候，只能选择开始时间之后的时间
var start = laydate.render({ // 开始时间
  elem: '#startElemInput',
  value: new Date(),  // 设置默认的为当前
  done: function (value, date) {
    endMax = end.config.max;
    end.config.min = date;
    end.config.min.month = date.month - 1;
  }
});
var end = laydate.render({ // 结束时间
  elem: '#endElemInput',
  done: function (value, date) {
    if ($.trim(value) == '') {
      var curDate = new Date();
      date = {
        'date': curDate.getDate(),
        'month': curDate.getMonth() + 1,
        'year': curDate.getFullYear()
      };
    }
    start.config.max = date;
    start.config.max.month = date.month - 1;
  }
});
form.render();
```



form表单中的select 如果是需要多选择下拉框的话    [formselect](https://hnzzmsf.github.io/example/example_v4.html) 

后续有一个[改进的多选框版本](https://maplemei.gitee.io/xm-select/#/basic/language)