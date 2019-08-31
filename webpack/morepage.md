# webpack多页面构建

项目开发中如果有多页面，每个页面都是加载单独的js，就需要做成多页面应用

核心思路：
1. 多页面就是将 `entry` 设置为一个对象，有多少文件就设置多少入口js文件
2. 页面一般是使用 `html-webpack-plugin` 来自动生成，每个页面设置一个对应的 `new HtmlWebpackPlugin({})`
3. 用nodejs遍历 src 目录下的文件，找出所有的入口文件，然后其他的配置和单页面是一样的，用代码来实现上诉的内容
4. 页面的html模版页面如果更新了，需要在页面中手动刷新才能看到效果
5. 启动`npm run dev`的时候，访问的时候`dist`文件夹，此时如果项目没有`index.html`的文件的话，会报错`Cannot GET /` 只能手动添加要访问的页面后缀


```js
entry: {
  demo: path.resolve(__dirname, '../src/demo/index.js'),
  test: path.resolve(__dirname, '../src/test/index.js'),
  wujun: path.resolve(__dirname, '../src/wujun/index.js'),
  vendor: ['axios']
},

new HtmlWebpackPlugin({
  title: 'wujun name',
  filename: 'demo.html',
  template: path.resolve(__dirname, '../src/demo/html/index.html'),
  favicon: path.resolve(__dirname, '../static/myico.48.ico'),
  // 该顺序就表示了 在html页面中 script 的顺序 ，配上下面的参数
  // 如果没有该参数，则打包出来的所有js都会加载到页面上
  chunks: ['vendor','demo'],
  chunksSortMode: 'manual',   //'none' \| 'auto' \| 'dependency' \| 'manual' \| {Function}
  inject: true,
  minify: {
    removeComments: true,
    collapseWhitespace: true
  }
}),

```