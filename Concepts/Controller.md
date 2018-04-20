# Controllers

原文: <http://loopback.io/doc/en/lb4/Controllers.html>

- 概念
  - review questions
- 操作
- 路由到控制器
- 指定控制器API
- 编写控制器方法
- 处理控制器中的错误

### 概念

Controller 是一个实现由 应用 API 定义的操作类。  
它实现应用的业务逻辑，并充当 HTTP/REST API 和 域、数据库 模型之间的桥梁。

将装饰添加 Controller 类以及其成员， 以将应用的 API 操作映射到相应的 控制器操作。  

一个 `Controller` 操作仅处理后端服务、数据库输入和抽象操作。  

##### Review questions

？？？



### 操作

在路由的操作示例中, 这个 `greet()` 操作被定义为一个普通的JavaScript函数。

下面的例子在TypeScript中将其作为Controller方法显示。

```js
// plain function Operation
function greet(name: string) {
  return `hello ${name}`;
}

// Controller method Operation
class MyController {
  greet(name: string) {
    return `hello ${name}`;
  }
}
```

### 路由到控制器

这是以下示例中使用的基本API规范。它是一个操作对象。

```js
const spec = {
  parameters: [{name: 'name', schema: {type: 'string'}, in: 'query'}],
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
```

> 有几种方法可以定义路由到控制器方法。

第一个示例没有任何魔法定义到Controller的路由。

```js
// ... in your application constructor
this.route('get', '/greet', spec, MyController, 'greet');
```

装饰器允许使用 路由元数据注释 controller 方法。 
所以 Loopback 可以为你调用 app.route() 函数。

```js
import {get} from '@loopback/rest';

class MyController {
  @get('/greet', spec)
  greet(name: string) {
    return `hello ${name}`;
  }
}

// ... in your application constructor
this.controller(MyController);
```

### 指定控制器 API

对于较大的 LoopBack 应用, 你可以使用 OpenAPI规范 将你的路由组织到 API规范中。  
这个 `@api` 修饰符接受一个类型为 ControllerSpec 的 spec， 它包含一个字符串 `basePath` 和 一个 `Paths` 对象注意它不是完全 `OpenAPI` 规范。

```js
// ... in your application constructor
this.api({
  openapi: '3.0.0',
  info: {
    title: 'Hello World App',
    version: '1.0.0',
  },
  paths: {
    '/greet': {
      get: {
        'x-operation-name': 'greet',
        'x-controller-name': 'MyController',
        parameters: [{name: 'name', schema: {type: 'string'}, in: 'query'}],
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
});
this.controller(MyController);
```

@api装饰器允许你用规范注释你的控制器，所以LoopBack可以为你调用app.api() 函数。

```js
@api({
  openapi: '3.0.0',
  info: {
    title: 'Hello World App',
    version: '1.0.0',
  },
  paths: {
    '/greet': {
      get: {
        'x-operation-name': 'greet',
        'x-controller-name': 'MyController',
        parameters: [{name: 'name', schema: {type: 'string'}, in: 'query'}],
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
})
class MyController {
  greet(name: string) {
    return `hello ${name}`;
  }
}
app.controller(MyController);
```


### 编写控制器方法

下面是一个使用几个内置助手的示例控制器(修饰符)，
这些助手给出了关于Controller方法的LoopBack提示。

```js
import {HelloRepository} from '../repositories';
import {HelloMessage} from '../models';
import {get, param} from '@loopback/rest';
import {repository} from '@loopback/repository';

export class HelloController {
  constructor(
    @repository(HelloRepository) protected repository: HelloRepository,
  ) {}

  // returns a list of our objects
  @get('/messages')
  async list(@param.query.number('limit') limit = 10): Promise<HelloMessage[]> {
    if (limit > 100) limit = 100; // your logic
    return await this.repository.find({limit}); // a CRUD method from our repository
  }
}
```

- `HelloRepository` 继承自 `Repository`, 这是 Loopback 的数据库抽象。
- `HelloMessage` 是随意对象, 返回一个 该对象的列表
- `@get('/messages')` 自动创建一个路径对象适用于OpenAPI规范， 它也处理请求路由。
- `@param.query.number` 在生成的规范中指定路由通过将作为数字的查询来获取参数。

##### 处理控制器中的错误

为了指定要抛出的控制器方法的错误，使用类HttpErrors。
HttpErrors是一个从http-errors重新导出的类.
并可以在@ loopback / rest包中找到.

下面列出的是一些最常见的错误代码。

```js
Status Code | Error
400           错误请求
401           认证错误
403           禁止访问
404           NotFound
500           服务器错误
502           BadGateway
503           服务不可用
504           超时
```

下面的例子显示了前面的控制器使用HttpErrors进行了修改，同时进行了一次测试以验证错误是否正确引发。


```js
// test/integration/controllers/hello.controller.integration.ts
import {HelloController} from '../../../src/controllers';
import {HelloRepository} from '../../../src/repositories';
import {testdb} from '../../fixtures/datasources/testdb.datasource';
import {expect} from '@loopback/testlab';
import {HttpErrors} from '@loopback/rest';

const HttpError = HttpErrors.HttpError;

describe('Hello Controller', () => {
  it('returns 422 Unprocessable Entity for non-positive limit', () => {
    const repo = new HelloRepository(testdb);
    const controller = new HelloController(repo);

    return expect(controller.list(0.4)).to.be.rejectedWith(HttpError, {
      message: 'limit is non-positive',
      statusCode: 422,
    });
  });
});

// src/controllers/hello.controller.ts
import {HelloRepository} from '../repositories';
import {HelloMessage} from '../models';
import {get, param, HttpErrors} from '@loopback/rest';
import {repository} from '@loopback/repository';

export class HelloController {
  constructor(
    @repository(HelloRepository) protected repo: HelloRepository,
  ) {}

  // returns a list of our objects
  @get('/messages')
  async list(@param.query.number('limit') limit = 10): Promise<HelloMessage[]> {
    // throw an error when the parameter is not a non-positive integer
    if (!Number.isInteger(limit) || limit < 1) {
      throw new HttpErrors.UnprocessableEntity('limit is non-positive');
    } else if (limit > 100) {
      limit = 100;
    }
    return await this.repo.find({limit});
  }
}
```