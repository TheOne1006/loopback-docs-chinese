# 上下文

原文: <http://loopback.io/doc/en/lb4/Context.html>

- 什么是上下文?
  - 为什么如此重要?
- 如何创建一个上下文
- 应用级别的上下文(全局的)
- 服务级别上下文
- 请求级别上下文(request)
- 从上下文中存储和检索项目
  - 在绑定键中编码值类型
- 依赖注入
- 上下文元数据和装饰糖


### 什么事上下文?

- 一个抽象表示应用的所有状态和依赖关系
- Loopback 利用上下文来管理所有东西
- 在应用中一个可以全局注册任何的东西(所有配置, 状态, 依赖, 类, 等)
- 用于向代码中注入依赖项的控制反转的容器(inversion of control container) ??。

##### 为什么如此重要?

可以使用可选项父上下文 和 可选项名称 创建一个上下文，

如果没有提供名字, 将生成一个 UUID 作为值。  

上下文实例可以使用父级链接来形成一个层次结构。

例如, 下面的代码会床间一个包含三个上下文的链: 
reqCtx -> serverCtx -> rootCtx

```js
import {Context} from '@loopback/context';

const rootCtx = new Context('root-ctx'); // 没有
const serverCtx = new Context(rootCtx, 'server-ctx'); // 作为父级
const reqCtx = new Context(serverCtx); // 没有执行名字, 将会生成一个 uuid
```

Loopback 的 上下文系统允许无线的创建 Context 实例, 其中每个可能有一个父级别的上下文。  

一个应用通常有三个 "级别" 的上下文, 应用级别, 服务级别, 以及 请求级别。

### 应用级别上下文(全局)

- 在整个应用程序的真个晟敏个周期中存储所有初始和修改应用状态。
- 通常在创建应用程序时配置(尽管其他事情可能在 运行中 修改上下文的内容)

这是一个案例:

```js
import {Application} from '@loopback/core';

// 请注意 Application 继承于 Context
const app = new Application(); // `app` is a "Context"
class MyController {}
app.controller(MyController);
```

在案例中, 使用 `.controller` 辅助方法注册一个新的 控制器，
需要注意的一点是 MyController 实际上已经注入到应用的上下文了


### 服务级别上下文


服务级别上下文:
  - 是应用级别上下文的子级
  - 保存特定的配置为指定的实例配置

你的应用通常包含一个或者多个服务器实例， 他们都使用 相同的 应用级别 上下文作为起父级。
这意味着除非您直接替换服务器实例上的这些绑定，否则在应用程序上定义的任何绑定也可用于服务器。

例如: `@loopback/rest` 有一个 `RestServer` 类, 它在一个端口上建立了 http(s) 的服务，以及在该服务器上为 REST API 定义路由。
要为 `RestServer` 设置端口绑定， 您需要将 `RestBinding.PORT` 赋予一个数字。

我们可以有选择地为某些服务器实例重新绑定此值，以更改它们使用的端口:

```js
// src/application.ts
async start() {
  // publicApi will use port 443, since it inherits this binding from the app.
  app.bind(RestBindings.PORT).to(443);
  const publicApi = await app.getServer<RestServer>('public');
  const privateApi = await app.getServer<RestServer>('private');
  // privateApi will be bound to 8080 instead.
  privateApi.bind(RestBindings.PORT).to(8080);
  await super.start();
}
```


### 请求级别上下文(request)

以 `@loopback/rest` 为例, 我们可以创建自定义序列:

- 是为每个传入的服务器请求动态创建的
- 扩展应用程序级别上下文（以便在请求/响应生命周期中访问应用程序级别依赖性）
- 一旦发送响应就会收集垃圾（内存管理）

让我们看看这是action:

```js
import {DefaultSequence, RestBindings} from '@loopback/rest';

class MySequence extends DefaultSequence {
  async handle(request: ParsedRequest, response: ServerResponse) {
    // we provide these value for convenience (taken from the Context)
    // but they are still available in the sequence/request context
    const req = await this.ctx.get(RestBindings.Http.REQUEST);
    const res = await this.ctx.get(RestBindings.Http.RESPONSE);
    this.send(res, `hello ${req.query.name}`);
  }
}
```

- `this.ctx` 可用于序列
- 允许您使用应用程序中的资源以及实时请求可用资源制定您的响应（当您需要时）
