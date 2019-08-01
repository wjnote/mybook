## node测试
测试是目前软件中重要的一个功能。测试根据颗粒度不同分为

- 单元测试
- 接口测试
- 功能测试，又叫端到端测试（e2e)
- 压力测试

### 前端测试工作流
- Mocha: 是一个功能丰富的前端测试框架(运行测试的工具)。Mocha 既可以运行在 node 的环境，也可以运行在浏览器的环境。
- Jest： Facebook推出了的不需要繁琐的配置的测试工具，集成了断言库和覆盖率的报告
- Karma: 一个基于Node.js的JavaScript测试执行过程管理工具（Test Runner）,这个测试工具的一个强大特性就是，它可以监控文件的变化，然后自行执行，通过console.log显示测试结果。
- Travis.CI: 提供的是持续集成服务（Continuous Integration，简称 CI）。它绑定 Github 上面的项目，只要有新的代码，就会自动抓取。然后，提供一个运行环境，执行测试，完成构建，还能部署到服务器。
- [继续集成](http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html)

### 断言库
做单元测试是需要写测试脚本的，那么测试脚本就需要用到断言库，对于前端单元测试来说，有以下常用断言库

- [assert](http://nodejs.cn/api/assert.html) ： 这是nodejs中的断言模块
- [should.js](https://github.com/shouldjs/should.js)
- [expect.js](https://github.com/Automattic/expect.js)
- [chai.js](https://www.chaijs.com/),是Mocha 常见搭配的断言库

### 测试覆盖率报告
mocha 自身不集成 测试覆盖率 功能。这项功能能让我们直观地从数字上感受到我们测试覆盖的情况和效果。一般使用[nyc](https://github.com/istanbuljs/nyc) 来配合生成报告 `nyc --reporter=html --reporter=text mocha` 这样会生成所需的报告

Jest 自身集成了 istanbul，你所需要关心的也只是在运行测试的时候多加一个 --coverage 选项而已

### 测试环境
当你需要测试的部分逻辑需要使用到浏览器才具备的一些 API，例如：document、location 以及 DOM 操作等等浏览器环境才具备的 API。此时我们不可避免地需要 mock 这些实现。[jsdom](https://github.com/jsdom/jsdom)

Jest 依然自身集成了 jsdom，且默认开启。可以通过 Configuring Jest · Jest 中的配置项改变默认配置。

### mock
单元测试不可避免得需要用到 mock，在这里 mock 的大致意思是：屏蔽一个函数或一个模块的方法（属性）原有实现或值，然后使用我们期望的实现或值代替它。

mocha 本身并没有支持这一相关的功能，都是采用插件来完成

Jest 自身集成了对 mock 的支持。使用 Jest 编写测试时，在测试代码中可通过全局对象 jest 中的属性方法 fn 创建一个 spy，这是针对方法的 spy。还有一个类似的是 jest.spyOn ，用于生成一个作用于对象方法的 spy 或 mock。
>无论是 mocha 或者 Jest 本身在 mock 的过程中都会遇到一个问题：如何 mock 模块中一个没有被导出的私有变量或方法,可以使用[plugin-rewire](https://github.com/speedskater/babel-plugin-rewire)来解决


### Snapshot
这个特性算是 Jest 的王牌之一了，它能够记住你当时所测试对象（可以是对象、React element 等）的结构并输出成一个单独的 xxx.snap 文件用于保存结构。对于对象就会生成一个类似 JSON 的结构，如果是 React Element 的话会生成一个类似于 DOM 的结构。

***
其中使用 Jest 编写测试会方便很多，减少了很多繁琐的配置，几乎是零配置（只需要少量简单的配置即可）。同时 snapshot 以及 mock 等功能极大的节省了我测试的时间，即使你不是使用 React 也是一样能享受到它所有的有点，它与 React 并没有依赖关系。