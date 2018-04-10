# Todo 教程

- 简介
- 开始
- 教程
  - 步骤
- 试一试
  - Stuck？
  - bug/反馈
- 贡献
- 测试
- 所有的贡献者
- License

展示一个基础教程让你快速入门 loopback4!

### 简介

教程演示如何使用 loopback4 创建一个基础的获取 todo 列表的 API.

### 开始

你需要确保已经安装了一些东西:

- Node.js 8.x 以上

另外, 本教程假定你已经掌握了一些技术,语言和概念

- JavaScript(ES6)
- REST

最后需要你安装 loopback4 的客户端工具:

```bash
npm i -g @loopback/cli
```

### 教程

跟着本教程，从[创建你的脚手架](/Examples-and-tutorials/itodo-tutorial/create-your-app-scaffolding.md) 开始。

##### 步骤

- [创建你的脚手架](/Examples-and-tutorials/itodo-tutorial/create-your-app-scaffolding.md)
- [添加legacy juggler](/Examples-and-tutorials/itodo-tutorial/adding-legacy-juggler.md)
- [添加你的 Todo 模型](/Examples-and-tutorials/itodo-tutorial/add-todo-model.md)
- [添加数据源](/Examples-and-tutorials/itodo-tutorial/add-a-datasource.md)
- [添加存储](/Examples-and-tutorials/itodo-tutorial/add-a-repository.md)
- [添加控制器](/Examples-and-tutorials/itodo-tutorial/add-a-controller.md)
- [把它们放在一起](/Examples-and-tutorials/itodo-tutorial/putting-it-all-together.md)


### 试一试

如果你希望看到本教程最终的结果作为案例, 跟随一下步骤:

1. 运行 `lb4 example` 命令选择和克隆 todo 的资源:
  ```bash
  $ lb4 example
  ? What example would you like to clone? (Use arrow keys)
  ❯ todo: Tutorial example on how to build an application with LoopBack 4..
    hello-world: A simple hello-world Application using LoopBack 4
    log-extension: An example extension project for LoopBack 4
    rpc-server: A basic RPC server using a made-up protocol.
  ```
2. 调到目录以及安装相关依赖

```bash
cd loopback4-example-todo && npm i
```

3. 最后,启动应用
```bash
$ npm start

```

随意浏览应用的代码，以了解它是如何工作的, 或者 学习如何逐步的构建它,然后继续本课程。


### 其他(略)
