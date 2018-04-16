# 添加控制器

原文: <https://loopback.io/doc/en/lb4/todo-tutorial-controller.html>

- 控制器
- 创建你的控制器
  - src/controllers/todo.controller.ts

### 控制器

在 LoopBack4, 控制器处理 api 请求-响应 的声明周期。  

控制器上的每个方法 都可以单独寻址 以处理传入请求(例如 post 处理 /todo),执行业务逻辑, 然后返回相应。  

在这方面, 控制器是你大部分业务逻辑 将会在这里。


### 创建你的控制器

所以, 让我们创建一个控制器来处理我们的 Todo 路由,  

在 `src/controllers` 目录中创建一下两个目录:

- `index.ts` (辅助暴露)
- `todo.controller.js`

除了自己创建的函数之外,
我们还可以将添加设置路由的装饰器以及传入请求的参数。  

首先我们需要定义我们基本的控制器类以及 使用的存储,
我们将用它来对数据源执行我们的操作。  


##### src/controller/todo.controller.ts

```js
import {repository} from '@loopback/repository';
import {TodoRepository} from '../repositories';

export class TodoController {
  constructor(
    @repository(TodoRepository.name) protected todoRepo: TodoRepository,
  ) {}
}
```

这个 `@Repository` 修饰符将检索 以及注入一个 `TodoRepository` 实例 在任何入站请求的时候。  

控制器对象的生命周期是每个请求，这意味着为每个请求创建一个新的控制器实例,

因此，我们希望注入我们的 Todo Repository, 因为创建这些实例 比 创建新的控制器实例更加复杂， 更昂贵。

> 注意:
  你可以自定义 loopback 4 所有绑定的声明周期， 控制器可以轻松的使用 单声明周期来降低启动成名本，
  查看更多信息: <https://loopback.io/doc/en/lb4/Dependency-injection.html>

现在我们已经建立了库连接，让我们创建我们的第一个处理函数。

##### src/controller/todo.controller.ts

```js
import {repository} from '@loopback/repository';
import {TodoRepository} from '../repositories';
import {Todo} from '../models';
import {HttpErrors, post, param, requestBody} from '@loopback/rest';

export class TodoController {
  constructor(
    @repository(TodoRepository) protected todoRepo: TodoRepository,
  ) {}

  @post('/todo')
  async createTodo(@requestBody() todo: Todo) {
    if (!todo.title) {
      return Promise.reject(new HttpErrors.BadRequest('title is required'));
    }
    return await this.todoRepo.create(todo);
  }
}
```

在这个案例中， 我们是用了 两个新的关于路由的修饰符， 用于处理请求方式 和请求的主体

- `@post('/todo')` 由 `@loopback/rest` 提供，以便在路径和请求方式匹配时它可以将请求重定向到此函数。
- `@requestBody()` 将 Todo 的 openAPI 模式与请求主题关联， 以便Loopback 可以验证请求的格式。
(注意,撰写本文时, 模型定义验证`schematic validation` 尚未实现)

我们自己添加了验证逻辑，以确保 如果未获得post 请求提供的 title 字段, 将会抛出错误。

让我们用其他的修饰符去创建一个 REST API


##### src/controller/todo.controller.ts

```js
import {repository} from '@loopback/repository';
import {TodoRepository} from '../repositories';
import {Todo} from '../models';
import {
  HttpErrors,
  post,
  param,
  requestBody,
  get,
  put,
  patch,
  del,
} from '@loopback/rest';

export class TodoController {
  constructor(
    @repository(TodoRepository) protected todoRepo: TodoRepository,
  ) {}

  @post('/todo')
  async createTodo(@requestBody() todo: Todo) {
    if (!todo.title) {
      return Promise.reject(new HttpErrors.BadRequest('title is required'));
    }
    return await this.todoRepo.create(todo);
  }

  @get('/todo/{id}')
  async findTodoById(@param.path.number('id') id: number): Promise<Todo> {
    return await this.todoRepo.findById(id);
  }

  @get('/todo')
  async findTodos(): Promise<Todo[]> {
    return await this.todoRepo.find();
  }

  @put('/todo/{id}')
  async replaceTodo(
    @param.path.number('id') id: number,
    @requestBody() todo: Todo,
  ): Promise<boolean> {
    // REST adapter does not coerce parameter values coming from string sources
    // like path & query, so we cast the value to a number ourselves.
    id = +id;
    return await this.todoRepo.replaceById(id, todo);
  }

  @patch('/todo/{id}')
  async updateTodo(
    @param.path.number('id') id: number,
    @requestBody() todo: Todo,
  ): Promise<boolean> {
    id = +id;
    return await this.todoRepo.updateById(id, todo);
  }

  @del('/todo/{id}')
  async deleteTodo(@param.path.number('id') id: number): Promise<boolean> {
    return await this.todoRepo.deleteById(id);
  }
}
```

以上示例需要一些注意事项:

- `@get('/todo/{id}') ` 这种路由, 可以使用 `@param.path` 修饰符在请求是将这些值注入到处理函数中。
- loopback 的 `@param` 装饰符 还由其他子修饰符 组成例如 `@param.path`, `@param.query`, `@param.header` 它允许REST  请求的这些部分成为参数。
- loopback 的 `@param.path` 和 `@param.query` 还提供自修饰符用于指定某些参数的类型。 例如 `@param.path.number('id')`

现在我们已经创建了控制器， 最后一步我们把他们全部整合到 应用中。