# 添加存储

原文:<https://loopback.io/doc/en/lb4/todo-tutorial-repository.html>

- 存储
- 创建你的存储
  - src/repositories/todo.repository.ts

### 存储

存储模式 在 LoopBack 3 和 4 中有非常大的差异。  
在 LoopBack 3 你 使用 Model 类 定义自己的 CURD 操作。  
在 LoopBack4 中 负责此事的层已从模型本身的定义中分离出来。 放到了 存储层中处理


### 创建你的存储

在 `src/repositories` 目录中创建两个文件:
  - `index.ts` 帮助暴露文件
  - `todo.repository.ts`


这个 `TodoRepository` 将继承 来自 `@loopback/repository` 的 `DefaultCrudRepository` 类,
并将定义我们正在使用的模型类型，以及它的 ID 类型。
它将自动赋予 基本的 CURD 数据库操作。


##### src/repositories/todo.repository.ts

```js
import {DefaultCrudRepository, DataSourceType} from '@loopback/repository';
import {Todo} from '../models';
import {inject} from '@loopback/core';

export class TodoRepository extends DefaultCrudRepository<
  Todo,
  typeof Todo.prototype.id
> {
  constructor(@inject('datasources.db') protected datasource: DataSourceType) {
    super(Todo, datasource);
  }
}
```
