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

```
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
  查看更多查看 <https://loopback.io/doc/en/lb4/Dependency-injection.html>
