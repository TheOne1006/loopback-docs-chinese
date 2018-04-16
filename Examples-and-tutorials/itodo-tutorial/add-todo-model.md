# 添加 Todo 模型

原文: <https://loopback.io/doc/en/lb4/todo-tutorial-model.html>

- 模型
- 创建你的 Todo 模型
  - src/models/todo.model.ts

### Models

现在我们可以开始处理 Loopback4 应用的数据关系了,   
为此, 我们继续创建 Todo model 用于我们即将需要的 Todo list。   
这个 Todo model 将提供一个 [数据转换对象](https://en.wikipedia.org/wiki/Data_transfer_object) (也被成为 DTO) 用于根据请求表示传入的Todo实例，
以及我们用于Legacy Juggler的数据结构。

> 注意!
  loopback 3 将 Model 作为 “核心操作”, 在 Loopback 4 中 已不再是这样。
  而 loopback4 中提供了许多帮助方法 和 装饰器，允许以类似的方法使用模型。


### 创建你的 TODO Model

一个代办列表 包含 所有跟踪任务。它需要给任务加标签，以便于区分它们, 这是有用的，添加额外的信息来描述这些任务,  
最后，提供一种跟踪他们是否完整的方法

例如：
我们创建 Todo 模型 表示 我们的 Todo 实例， 它需要:

- 一个唯一ID
- 一个标题
- 描述文字用来表示代办事情
- 一个 boolean 来表示是否完成 该任务

在 `src/models` 目录中, 需要创建 两个文件:

- index.ts
- todo.model.ts

> 注意!
  `index.ts` 文件是一个辅助文件, 随着您项目中模型数量的增长，这种模式可以节省大量时间,
  因为它允许您在尝试从目标文件夹内的文件导入类型时指向该目录.


对于Legacy Juggler来说，了解如何使用我们的模型类, 它将需要扩展 `Entity` 类,
以及为getId函数提供重写，以便它可以根据需要检索Todo模型的ID。



src/models/todo.model.ts

```js
import {Entity, property, model} from '@loopback/repository';

@model()
export class Todo extends Entity {
  @property({
    type: 'number',
    id: true,
  })
  id?: number;

  @property({
    type: 'string',
    required: true,
  })
  title: string;

  @property({
    type: 'string',
  })
  desc?: string;

  @property({
    type: 'boolean',
  })
  isComplete: boolean;

  getId() {
    return this.id;
  }
}
```

现在我们有了我们的模型，现在是时候添加一个数据源，以便我们可以执行真正的CRUD操作！
