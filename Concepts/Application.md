# Application

- 什么是 应用?
- 创建你自己的应用类
- 定义你的应用
  - 配置项
    - 组件
    - 控制器
    - 服务
  - 构建器配置
- 注意事项
  -  使用RestServer时从RestApplication扩展
  - 使用唯一绑定
  - 避免使用getSync
  - 请谨慎使用单例绑定作用域

### 什么是 Application

在 Loopback4 中, `Application` 类 是中心类 串起模块组件， 控制器， 服务和绑定。
`Application` 扩展自 `Context`, 还提供 启动和停止 自身的相关服务。

在使用 Loopback4 是， 我们强烈建议使用创建自己的子类，用于更好的组织和配置。  

### 创建自己的 Application 类

通过创建自己的 Application 类，你可以执行额外的任务。

- 将配置信息传递给基类构造函数
- 应用启动前执行一些异步的连线
- 应用停止时执行一些优雅的清理

##### src/wedget.application.ts

```js
import {Application} from '@loopback/core';
import {RestComponent} from '@loopback/rest';
import {UserController, ShoppingCartController} from './controllers';

export class WidgetApplication extends Application {
  constructor() {

    // 将你的配置通过这种方式传递给构造函数
    super({
      rest: {
        port: 8080,
      },
    });

    const app = this; // 为了更清晰
    // 你可以在此绑定到应用程序级别的上下文。
    // app.bind('foo').to(bar);
    app.component(RestComponent);
    app.controller(UserController);
    app.controller(ShoppingCartController);
  }

  async stop() {
    // 停下脚步之前你需要做的事情
    // app (graceful closing of connections, flushing buffers, etc)
    console.log('Widget application is shutting down...');
    // 超类停止方法将在所有服务器上调用stop    
    //绑定到应用程序。
    await super.stop();
  }
}
```

### 定义你的应用

你的引用可以使用构造函数的参数，实例方法绑定 或者两者结合进行配置。

##### 绑定配置

绑定是是整个示例最常见的应用程序配置形式，并且是建立应用改程序的推荐方法.  

除了 `Context` 提供的绑定功能外, `Application` 为提供了一些绑定的函数语法糖用于厂用电行为的绑定, 例如 `component`, `server`, `controller`

```js
export class MyApplication extends Application {
  constructor() {
    super();
    this.component(MagicSuite);
    this.server(RestServer, 'public');
    this.server(RestServer, 'private');

    this.controller(FooController);
    this.controller(BarController);
    this.controller(BazController);
  }
}
``` 
你可以在 `Application` API 文档找到相关的方法说明。  

另外， 你可以使用更高级的绑定形式来微调应用程序的配置:

```js
export class MyApplication extends Application {
  constructor() {
    super();
    this.server(RestServer);
    this.controller(FooController);
    this.bind('fooCorp.logger').toProvider(LogProvider);
    this.bind('repositories.widget')
      .toClass(WidgetRepository)
      .inScope(BindingScope.SINGLETON);
  }
}
```

上述案例中:
  - 注入调用fooCorp.logger将由LogProvider类处理。
  - 注入调用repositories.widget将由WidgetRepository类的单例实例处理


##### 组件

```js
app.component(MyComponent);
app.component(RestComponent);
```

组件函数允许在您的Application实例的上下文中绑定组件构造函数.  
[更多详情](/Using-components/init.md)

##### 服务

```js
app.server(RestServer);
app.servers([MyServer, GrpcServer]);
```

`server` 方法非常像以前的功能方法, 但通过 `servers()` 可以实现服务器批量绑定.  
上例中， 两个服务器实例粉饼绑定到用用程序的上下文 servers.public  和 servers.private


##### 配置构造函数

`Application` 类的构造函数提供一个 `ApplicationConfig` 应用配置 对象 里面 还包含了 组件级别配置例如 `RestServerConfig`, 
它还将自动创建绑定,然后通过依赖注入来注入. [详情查看](/Concepts/Dependency-injection.md)

> 注意: 绑定配置项 不能与 组件绑定, 提供器绑定, 作用于绑定 一起使用。(???)


### 注意事项

这里有些技巧可能帮助你避开一些坑和错误。

##### 使用RestServer时从RestApplication扩展

如果你想用 `@loopback/rest` 包总的 RestServer，我们推荐你的应用扩展 `RestApplication`  而不是手动绑定 `RestServer` 或者 `RestComponent`.  

`RestApplication` 已经使用 `RestComponent` 并在 `RestServer` 中生成有用的函数， 如应用级别提供的 `handler()`.  

这也意味着 你可以调用这些 `RestServer` 函数在应用构造器中执行所有 服务级别的 设置，而无需显示检索服务器的实例。


##### 使用唯一的绑定

使用绑定名称, 该名称前缀是一个与 loopback 不重叠的唯一字符串。  

案例中如应用中是为了 雇主 FooCorp 打造的, 你可以绑定前缀 `fooCorp`

```js
// 这不太可能与其他组件开发人员使用的key相冲突
// 避开 loopback 本身含有的 前缀
app.bind('fooCorp.widgetServer.config').to(widgetServerConfig);
```

##### 避免使用 getSync

我们提供的 `getSync` 方法不适合在异步检索绑定的情况，如构造函数体中。

但是， 必须执行此操作的场景有限， 尽量更避免使用, 

请使用 `get` 异步函数进行检索绑定。  

##### 请谨慎使用单例绑定作用域


默认情况下，控制器的绑定将在实例化一个新实例，
无论它们是从绑定中注入还是从中检索。
当应用程序有意义时，
应用程序应该只在控制器上设置 单例的 绑定范围。 (??)


