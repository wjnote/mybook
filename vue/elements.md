# elements使用技巧

1. el-form-item, prop绑定的值，必须可以通过 . 的方式从el-form上的model绑定的对象中找到， model绑定的数据必须是JSON对象格式，（不是必须的，只是JSON的数据，element-ui的验证功能才能实现）
```js
// 下面的方式可以使用循环的方式来展示form格式
  <template>
    <el-form :moel="formData" ref="formData" style="">
      <el-form-item :prop="'list['+index+'].name'" :rules="{validator: rule_name, trigger:'blur'}">
        <el-input v-model="item.name" auto-complete="off"></el-input>
      </el-form-item>
    </el-form>
  </template>

  formData:{
    list:[{name:'',age:''}]
  }

  rule_name(rule,value,callback){
    if(!value){
      return callback(new Error())
    }
    callback();
  }
```

2. 按钮要写在 `el-form-item` 中，提交方法要使用ui提供的方法，`element-ui`才能在提交前进行表单验证
```js
submit(formName){
  this.$refs[formName].validate(valid=>{
    if(!valid){
      // 没通过验证
    }
    // 通过验证
  })
}
```

3. form的取值和赋值，主要是赋值的时候需要注意表单不同类型赋值是不同的
取值： 获取form表单的值, `this.formName.key`
赋值： 大部分赋值都是使用 `this.formName.key = value`

    > 一般的赋值和显示都是一致的，但是类似下拉选择，时间date， 单选等


4. 首先想让他变成路由导航，el-menu 一定要添加 router 属性，此时他就会根据 el-menu-item 中的 index 去 push router,,,需要注意的是：多级菜单时，虽然 el-submenu 并不想让他跳转，但是一定要加上 index 属性（可以放index就行，保证唯一性）,并且index 属性的值一定要是 string ，比如 subIndex 拼接个 ‘’ ，达到转换字符串的目的

5. 表格中有些值是码值类型，需要我们填充表单的值的时候转换一下其中的值，类似于性别类的
```js
<el-table
      :data="tableData"
      ref="singleTable"
      highlight-current-row
      style="width: 100%">
      <!-- 这个是table的默认值，是前面的序号，不需要设置什么 指示type 为index即可 -->
      <el-table-column
        type="index"
        :index = "indexMethod"
        width="50">
      </el-table-column>
      <el-table-column
        prop="date"
        label="日期"
        width="180"
        >
      </el-table-column>
      <!-- 其中的值会经过 formatSex 的计算设置 -->
      <el-table-column
        prop="sex"
        label="性别"
        width="180"
        :formatter="formatSex"
        >
      </el-table-column>
</el-table>
{
  myIndex: 6,
  date: '2016-05-02',
  name: '王小虎',
  address: '<i>上海市普陀区金沙江路 1518 弄</i>',
  sex: 0
}
// methods 中
formatSex: function (row, column) {
      return row.sex === 1 ? '男' : row.sex === 0 ? '女' : '未知'
}
```
---

参考博客列表

[element-ui库的表单元素的总结](https://juejin.im/post/5dad488f518825420a281d2f)