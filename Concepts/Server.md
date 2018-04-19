# Server

原文链接: <http://loopback.io/doc/en/lb4/Server.html>

- 概念
- 用法
- 配置
  - 添加一个服务到应用实例


### 概念

服务接口定义了 为Loopback 应用实现的最小必要的功能(启动 和 停止)。  

Loopback 中的服务用于 入站传输 和 协议的实现 例如 基于 http 的 REST, 基于 HTTP2 的 gRPC, 基于 HTTP2 的 graphql 等,  
他们通常会监听特定端口上的请求, 处理他们，并返回适当的相应。  

单个应用可以有多个 服务实例 监听不同端口 并使用不同的协议。


### 用法

Loopback4 目前提供 `@loopback/rest` 包， 它提供一个基于 HTTP 的服务实现， 通过 REST 调用 RestServer 处理请求。  

想要在应用中使用它， 你需要 让你的 Application 类 扩展于 RestApplication, 
它还将为你的实例监听端口3000。

下面将展示如何使用

```js
import {RestApplication, RestServer} from '@loopback/rest';

export class HelloWorldApp extends RestApplication {
  constructor() {
    super();
    // 给我们的 RestServer 实例赋予一个序列处理函数
    // 所有的请求都将返回 'Hello World' 字符串作为相应
    // 在 RestApplication 中,可以注册 handler(处理函数) 方法， app 级别
    this.handler((sequence, request, response) => {
      sequence.send(response, 'Hello World!');
    });
  }

  async start() {
    // 调用 application 类的 启动法法，然后全部服务开始注册
    // call start on application class, which in turn starts all registered
    // servers
    await super.start();

    // get a singleton HTTP server instance
    // 获得一个 http 服务的实例
    const rest = await this.getServer(RestServer);
    console.log(`REST server running on port: ${await rest.get('rest.port')}`);
  }
}

```

### 配置

你可以通过 `app.server()` 方法, 添加一个服务实例, 也可以使用 `app.servers([])` 添加多个.  

使用 `app.server()` 可以确保绑定唯一服务实例命名。  

下面将演示如何使用这些函数:

```js
import {Application} from '@loopback/core';
import {RestServer} from '@loopback/rest';

export class HelloWorldApp extends Application {
  constructor() {
    super();
    // 该服务器实例将被绑定在 servers.fooServer 下
    this.server(RestServer, 'fooServer');
    // Creates a binding for "servers.MQTTServer" and a binding for
    // "servers.SOAPServer";
    this.servers([MQTTServer, SOAPServer]);
  }
}
```