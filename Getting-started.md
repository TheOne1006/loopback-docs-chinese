# 快速开始

- 条件
- 安装 loopback 4 客户端
- 创建一个新项目
  - 启动项目
- 添加你自己的 controller

### 条件

安装 [Node.js](https://nodejs.org/en/download/)(8.0 版本以上)


### 安装 loopback 4 客户端

loopback 4 客户端是一个命令行工具, 可以搭建一个项目 或者 添加更多功能扩展。  
客户端提供快速的方法开始 loopback4 项目, 并且保持最佳实践。  


安装命令如下:

```bash
npm i -g @loopback/cli
```


### 创建一个新项目

客户端工具将会构建 项目, 配置 TypeScript 编译 以及安装所有相关的依赖。

执行如下命令将会创建一个新项目

```bash
lb4 app
# 交互式的创建
? Project name: getting-started #项目名称
? Project description: Getting started tutorial #项目描述
? Project root directory: (getting-started) #项目的根目录
? Application class name: StarterApplication # 项目类名
# 选择需要的项目设置
? Select project build settings: Enable tslint, Enable prettier, Enable mocha, Enable loopbackBuild

```


##### 启动项目

初始项目有一个 "ping" 的路由，用于测试, 运行方式如下:  

```bash
cd getting-started
npm start
```

在浏览器中, 访问 <http://127.0.0.1:3000/ping>

### 添加你自己的controller

现在我们已经拥有了一个基础的项目，是时候添加我们自己的 controller 了。  
让我们添加一个简单的 "Hello World" controller 如下:

1. 在 `/src/controllers` 目录下 创建一个 `hello.controller.ts` 的文件
2. 添加一下内容到文件中
  ```js
  import {get} from '@loopback/rest';

  export class HelloController {
    @get('/hello')
    hello(): string {
      return 'Hello world!';
    }
  }
  ```
3. 使用 `npm start` 启动应用
  - 注意: 如果应用正在运行中, 使用 `CTRL + C` 终止它
4. 访问 < http://127.0.0.1:3000/hello> 可以看见 `Hello world!` 字样
