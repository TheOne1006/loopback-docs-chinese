# 依赖注入

原文: <http://loopback.io/doc/en/lb4/Dependency-injection.html>

- 介绍
- 配置注入的信息
- 依赖注入的方式
  - 构造器的注入
  - 属性注入
  - 方法注入
- 可选的依赖
- 循环依赖
- 其他资源

### 介绍

依赖注入是一种将类或函数的依赖关系与其行为分离的技术，以便使代码松散耦合。

例如， 序列方法 `authenticate` 支持不同的认证策略(例如 http 的 Auth，OAuth2 等), 不是对某种查找表进行硬编码以找到正确的策略实例，
authenticate使用依赖注入来让调用者指定使用哪种策略。

下面的例子显示了认证操作的简化实现，请参阅完整工作版本的@ loopback / authenticate源代码。

```js
class AuthenticateActionProvider {
  constructor(@inject(AuthenticationBindings.STRATEGY) strategy) {
    this.strategy = strategy;
  }

  value(): AuthenticateFn {
    return request => this.action(request);
  }

  // this is the function invoked by "authenticate()" sequence action
  action(request: ParsedRequest) {
    const adapter = new StrategyAdapter(this.strategy);
    const user = await adapter.authenticate(request);
    return user;
  }
}
```

依赖注入使代码更容易扩展和自定义, 因为应用程序开发人员可以轻松地重新连接依赖关系,
它使代码更易于单独测试. 因为测试可以注入一个自定义版本的依赖项.
测试与外部服务（如数据库或OAuth2提供程序）交互的代码时，这一点尤其重要,
测试可以提供返回预定义响应的轻量级实现，而不是制作昂贵的网络请求.


### 配置注入的信息

现在我们编写一个获取注入依赖关系的类，你可能想知道这些值将从哪里注入，以及如何配置应该注入的内容。

在LoopBack中，我们使用Context来跟踪所有可注入的依赖关系。

```js
// TypeScript example

import {BasicStrategy} from 'passport-http';
import {RestApplication, RestServer} from '@loopback/rest';
// basic scaffolding stuff happens in between...

// The REST server has its own context!
const server = await app.getServer(RestServer);
server.bind(AuthenticationBindings.STRATEGY).to(new BasicStrategy(loginUser));

function loginUser(username, password, cb) {
  // check that username + password are valid
}
```

但是，当您想要创建一个绑定来实例化一个类并自动注入所需的依赖关系时，则需要使用.toClass（）方法


```js
server
  .bind(AuthenticationBindings.AUTH_ACTION)
  .toClass(AuthenticateActionProvider);

const provider = await server.get(AuthenticationBindings.AUTH_ACTION);
// provider 是 AuthenticateActionProvider 的实例
// provider.strategy 被设置为返回的值通过 server.get('authentication.strategy')
```

当通过创建绑定 `.toClass()`, Context 将会创建一个该类的新实例包含这个绑定, 并通过 `@inject` 装饰器注入构造函数参数和属性值。

注意, 要注入的依赖关系本身可以是类，在这种情况下， Context 将首先地柜实例化这些类, 根据需要解析他们的依赖关系。

在这个特定的例子中，这个类是一个Provider。提供者允许您定制上下文创建值的方式，可能取决于其他上下文值。提供者通常使用.toProvider（）API绑定

```js
app
  .bind(AuthenticationBindings.AUTH_ACTION)
  .toProvider(AuthenticateActionProvider);

const authenticate = await app.get(AuthenticationBindings.AUTH_ACTION);
// authenticate is the function returned by provider's value() method
```


### 依赖注入的方式

loopback 支持三种方式依赖注入:

1. 构造函数注入: 依赖提供作为构造函数的参数
2. 属性注入: 构造类完成后，依赖关系存储在实例属性中
3. 方法注入: 依赖作为方法调用参数, 沟槽函数注入是一种特殊的方法注入形式， 通过调用它的构造函数来实例化一个类 （？？？）

##### 构造函数注入

这是最常见的方式，应该是你的默认选择。

```js
class ProductController {
  constructor(@inject('repositories.Product') repo) {
    this.repo = repo;
  }

  async list() {
    return await this.repo.find({where: {available: true}});
  }
}
```

##### 属性注入

属性注入通常用于可选的依赖项，这些依赖项对于类的运行或对于具有合理默认值的依赖项不是必需的。

```js
class InfoController {
  @inject('logger', {optional: true})
  private logger = ConsoleLogger();

  status() {
    this.logger.info('Status endpoint accessed.');
    return {pid: process.pid};
  }
}
```

##### 方法注入

方法注入允许在方法调用级别注入依赖项。参数使用@inject或其他变体进行修饰，以将依赖项声明为方法参数。

```js
class InfoController {
  greet(@inject(AuthenticationBindings.CURRENT_USER) user: UserProfile) {
    return `Hello, ${userProfile.name}`;
  }
}
```

### 可选的依赖

有时候, 依赖关系是可选的。 例如, Logger 提供应用的日志记录级别可以有默认值.

要解析可选的依赖项，请将可选标志设置为true: 

```js
const ctx = new Context();
await ctx.get('optional-key', {optional: true});
// returns `undefined` instead of throwing an error
```

以下是另一个使用默认值属性显示可选依赖项注入的示例:

```js
// Optional property injection
export class LoggerProvider implements Provider<Logger> {
  // Log writer is an optional dependency and it falls back to `logToConsole`
  @inject('log.writer', {optional: true})
  private logWriter: LogWriterFn = logToConsole;

  // Log level is an optional dependency with a default value `WARN`
  @inject('log.level', {optional: true})
  private logLevel: string = 'WARN';
}
```

可选的依赖关系也可以用于构造函数和方法注入。
一个显示可选构造函数注入实例的示例:

```js
export class LoggerProvider implements Provider<Logger> {
  constructor(
    // Log writer is an optional dependency and it falls back to `logToConsole`
    @inject('log.writer', {optional: true})
    private logWriter: LogWriterFn = logToConsole,

    // Log level is an optional dependency with a default value `WARN`
    @inject('log.level', {optional: true})
    private logLevel: string = 'WARN',
  ) {}
}
```

可选方法注入的示例，其中prefix参数是可选的:

```js
export class MyController {
  greet(
    @inject('hello.prefix', {optional: true})
    prefix: string = 'Hello',
  ) {
    return `${prefix}, world!`;
  }
}
```

### 循环依赖

LoopBack可以检测循环依赖并报告导致问题的路径。
考虑下面的例子:

```js
import {Context, inject} from '@loopback/context';

interface Developer {
  // Each developer belongs to a team
  team: Team;
}

interface Team {
  // Each team works on a project
  project: Project;
}

interface Project {
  // Each project has a lead developer
  lead: Developer;
}

class DeveloperImpl implements Developer {
  constructor(@inject('team') public team: Team) {}
}

class TeamImpl implements Team {
  constructor(@inject('project') public project: Project) {}
}

class ProjectImpl implements Project {
  constructor(@inject('lead') public lead: Developer) {}
}

const context = new Context();

context.bind('lead').toClass(DeveloperImpl);
context.bind('team').toClass(TeamImpl);
context.bind('project').toClass(ProjectImpl);

try {
  // The following call will fail
  context.getSync('lead');
} catch (e) {
  console.error(e.toString());
}
```

当用户尝试解析“前导”绑定时，LoopBack检测到循环依赖并且输出以下错误：

```
Error: Circular dependency detected:
  lead --> @DeveloperImpl.constructor[0] -->
  team --> @TeamImpl.constructor[0] -->
  project --> @ProjectImpl.constructor[0] -->
  lead
```