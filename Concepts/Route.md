# 路由

原文: <http://loopback.io/doc/en/lb4/Routes.html>

- 概念
- 操作
- 参数
- 创建 REST 路由
  - 用API规范声明REST路由
  - 使用部分 OpenAPI 规范片段
  - 使用路由器装饰器和控制器方法
- 使用路线调用操作



### 概念

路由 是API 与操作的映射， 它告诉 Loopback 在 Http 请求的情况调用哪个操作。

Route 对象以及关联类型 作为 `@loopback/rest` 包的一部分提供。


### 操作

操作是接受参数的函数。 它们可以实现为普通的JavaScript / TypeScript函数（如http处理程序函数）或Controllers中的方法。

```js
// greet is a basic operation
function greet(name: string) {
  return `hello ${name}`;
}
```
### 参数

上面例子中, `name` 是一个 参数。参数是通常从序列请求中解析出来的值，然后作为参数传递给操作。 
使用OpenAPI规范将参数定义为路由的一部分。它们可以从请求的以下部分进行分析:

- body
- form data
- query string
- header
- path (url)

###  创建REST Routes

定义您的REST路线有三种不同的方法:

- 使用OpenAPI规范对象
- 在Route构造函数中使用部分OpenAPI规范片段
- 在控制器方法上使用路由装饰器

##### 用API规范声明REST路由

下面是一个Open API Specification的例子，它定义了与上例相同的操作。  
这是定义操作的声明性方法。

```js
import {RestApplication} from '@loopback/rest';
import {OpenApiSpec} from '@loopback/openapi-v3-types';

function greet(name: string) {
  return `hello ${name}`;
}

const spec: OpenApiSpec = {
  openapi: '3.0.0',
  info: {
    title: 'LoopBack Application',
    version: '1.0.0',
  },
  paths: {
    '/': {
      get: {
        'x-operation': greet,
        parameters: [{name: 'name', in: 'query', schema: {type: 'string'}}],
        responses: {
          '200': {
            description: 'greeting text',
            content: {
              'application/json': {
                schema: {type: 'string'},
              },
            },
          },
        },
      },
    },
  },
};

const app = new RestApplication();
app.api(spec);
```


### 使用部分 OpenAPI 规范 片段

下面的例子定义了一个 `Route`，它将匹配 `GET /`. 当 `Route` 匹配时, `greet` 操作将会被调用。  

它接受使用规范定义的 OpenAPI 操作对象。 
然后将该路由附加到在应用程序下运行的有效服务器上下文。

```js
import {RestApplication, Route} from '@loopback/rest';
import {OperationObject} from '@loopback/openapi-v3-types';

const spec: OperationObject = {
  parameters: [{name: 'name', in: 'query', schema: {type: 'string'}}],
  responses: {
    '200': {
      description: 'greeting text',
      content: {
        'application/json': {
          schema: {type: 'string'},
        },
      },
    },
  },
};

// greet is a basic operation
function greet(name: string) {
  return `hello ${name}`;
}

const app = new RestApplication();
const route = new Route('get', '/', spec, greet);
app.route(route); // attaches route to RestServer

app.start();
```

##### 使用路由器装饰器 装饰 控制球方法

您可以使用 `@loopback/rest` 中的动词装饰器函数来修饰控制器函数，以确定它们将处理哪些路线。


```js
// src/controllers/greet.controller.ts

import {get, param} from '@loopback/rest';

export class GreetController {
  // Note that we can still use OperationObject fragments with the
  // route decorators for fine-tuning their definitions and behaviours.
  // This could simply be `@get('/')`, if desired.
  @get('/', {
    responses: {
      '200': {
        description: 'greeting text',
        content: {
          'application/json': {
            schema: {type: 'string'},
          },
        },
      },
    },
  })
  greet(@param.query.string('name') name: string) {
    return `hello ${name}`;
  }
}

// src/index.ts
import {RestApplication} from '@loopback/rest';
import {GreetController} from './src/controllers/greet.controller';

const app = new RestApplication();

app.controller(GreetController);

app.start();
```

### 使用路线调用操作

这个例子分解了Sequences如何确定和调用任何给定请求的匹配操作。

```js
class MySequence extends DefaultSequence {
  async handle(request, response) {
    // find the route that matches this request
    const route = this.findRoute(request);

    // params is created by parsing the request using the route
    const params = this.parseParams(request, route);

    // invoke() uses both route and params to execute the operation specified by the route
    const result = await this.invoke(route, params);

    await this.send(response, result);
  }
}
```