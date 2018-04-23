# 序列

原文: <http://loopback.io/doc/en/lb4/Sequence.html>

- 什么是序列?
- 元素
- Actions
- 自定义序列
- 高级主题
  - 自定义序列操作
  - 查询字符串参数
  - 解析请求
  - 调用控制器方法
  - 编写 response
  - 发送错误
  - 保持你的序列

### 什么是序列

序列是一个无状态的动作分组，用于控制服务器如何响应请求。  

序列的规则很简单: 它必须产生对请求的响应,创建你自己的序列可以让你完全控制你的服务器实例如何处理请求和响应。DefaultSequence看起来像这样:

```js
class DefaultSequence {
  async handle(request: ParsedRequest, response: ServerResponse) {
    try {
      const route = this.findRoute(request);
      const params = await this.parseParams(request, route);
      const result = await this.invoke(route, params);
      await this.send(response, result);
    } catch (err) {
      await this.reject(response, err);
    }
  }
}
```

### 元素

上面案例总， `route`, `params` 以及 `reult` 都是元素。 当绑定序列, 您使用LoopBack元素来响应请求:

- Route
- Request
- Response
- OperationRetVal
- Params
- OpenAPISpec


### Actions 操作

Action 是指 只接受或者返回元素 的 javascript 函数。由一个 元素(动作)输入， 另一个 元素(动作)输出。

以下是内置 actions 列表:

```js
class MySequence extends DefaultSequence {
  async handle(request: ParsedRequest, response: ServerResponse) {
    // findRoute() 提供一个 route 元素
    const route = this.findRoute(request);
    // parseParams() 使用 route 元素 解析出 params 元素
    const params = await this.parseParams(request, route);
    // invoke() 调用 route 和 params 执行后 返回 结果 元素
    const result = await this.invoke(route, params);
    // send() uses the result element
    await this.send(response, result);
  }
}
```

### 自定义序列

大多情况可以通过 `DefaultSequence` 或者 稍微对其进行定制 就可以完成, 
如果我们的应用是通过命令工具 `lb4 app` 生成的,
那么默认的序列文件 `src/sequence.ts` 已经生成, 
你可以通过它轻松制定。

以下是应用在处理请求前和之后注销的实例:  

```js
import {DefaultSequence, ParsedRequest, ServerResponse} from '@loopback/rest';

class MySequence extends DefaultSequence {
  log(msg: string) {
    console.log(msg);
  }
  async handle(request: ParsedRequest, response: ServerResponse) {
    this.log('before request');
    await super.handle(request, response);
    this.log('after request');
  }
}
```

为了能在LoopBack 中使用你的 序列， 你需要 将它注册到你的应用中:

```js
import {RestApplication} from '@loopback/rest';
const app = new RestApplication();
app.sequence(MySequencce);

app.start();
```


### 高级主题

##### 自定义序列操作

可能会出现这样的情况，默认序列排序不是您想要更改的内容，而是序列将执行的单个操作。
 
为此，我们将通过将 Provider 绑定到 `RestBindings.SequenceActions.SEND` 这个 key 来 注册自定义发送操作 。  

首先，我们创建我们的CustomSendProvider类，它将在注入时提供send函数。

##### /src/providers/custom-send.provider.ts

```js
// send.provider.ts
import {Send, ServerResponse} from '@loopback/rest';
import {Provider, BoundValue, inject} from '@loopback/context';
import {
  writeResultToResponse,
  RestBindings,
  ParsedRequest
} from '@loopback/rest';

// Note: This is an example class; we do not provide this for you.
import {Formatter} from '../utils';

export class CustomSendProvider implements Provider<Send> {
  // In this example, the injection key for formatter is simple
  constructor(
    @inject('utils.formatter') public formatter: Formatter,
    @inject(RestBindings.Http.REQUEST) public request: ParsedRequest,
  ) {}

  value() {
    // Use the lambda syntax to preserve the "this" scope for future calls!
    return (response: ServerResponse, result: OperationRetval) => {
      this.action(response, result);
    };
  }
  /**
   * Use the mimeType given in the request's Accept header to convert
   * the response object!
   * @param ServerResponse response The response object used to reply to the
   * client.
   * @param OperationRetVal result The result of the operation carried out by
   * the controller's handling function.
   */
  action(response: ServerResponse, result: OperationRetval) {
    if (result) {
      // Currently, the headers interface doesn't allow arbitrary string keys!
      const headers = (this.request.headers as any) || {};
      const header = headers.accept || 'application/json';
      const formattedResult = this.formatter.convertToMimeType(result, header);
      response.setHeader('Content-Type', header);
      response.end(formattedResult);
    } else {
      response.end();
    }
  }
}
```

我们自定义的 Provider 将从请求头中的accept 获取, 并将数据结果转换成指定的 MIME 类型

接下来, 我们应用程序中，我们将注入 这个 Provider 到应用的 `RestBindings.SequenceActions.SEND` key 中。

```ts
import {RestApplication, RestBindings} from '@loopback/rest';
import {RepositoryMixin, Class, Repository, juggler} from '@loopback/repository';
import {CustomSendProvider} from './providers/custom-send.provider';
import {Formatter} from './utils';
import {BindingScope} from '@loopback/context';

export class YourApp extends RepositoryMixin(RestApplication) {
  constructor() {
    super();
    // Assume your controller setup and other items are in here as well.
    this.bind('utils.formatter').toClass(Formatter)
      .inScope(BindingScope.SINGLETON);
    this.bind(RestBindings.SequenceActions.SEND).toProvider(CustomSendProvider);
  }
}
```

因此，无论何时 `DefaultSequence` 调用该发送操作 ，它都会使用您的功能！您可以使用此方法来覆盖 `RestBindings.SequenceActions` 名称空间下列出的任何操作。

##### 查询字符串参数
##### 解析请求
##### 调用控制器方式
##### 编写回复
##### 发送错误
##### 保持你的序列

Work-in-progress