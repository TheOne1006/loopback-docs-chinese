# 核心概念

Loopback 4 中介绍了一些新的重要的概念：

  - [应用](/Concepts/Application.md): 用于设置 所有模块组件，控制器，服务器和绑定 的中心类。 Application 类 扩展于 [Context](/Concepts/Context.md) 以及提供 用于启动和停止相关服务的控件
  - [服务](/Concepts/Server.md): 实现 传输、协议 的入站， 例如 REST,http2,graphQL.  它通常用来监听特定的断点(协议/host/端口) , 处理传入请求，然后返回响应。
  - [上下文](/Concepts/Context.md): 应用中的状态和依赖的抽象, 让 Loopback 能够管理任何东西, 它将会在你的应用中全局注入(配置， 状态， 依赖， 类 等)
  - [依赖注入](/Concepts/Dependency-injection.md): 该技术用于将类或者函数的依赖关机的结构 与其行为分开以保持代码的松散耦合。
  - [控制器](/Concepts/Controller.md): 一个实现应用的 REST API 定义的操作类。它实现了应用的业务逻辑, 并充当  HTTP/ REST API  和 域/数据库模型 的桥梁。控制器只能处理 后端服务/数据库的处理输入和抽象。
  - [路由](/Concepts/Route.md): API规范与操作的映射。它告诉loopback http 请求 调用的是哪个对应的操作。
  - [序列](/Concepts/Sequence.md): 无状态的操作分组，用于控制服务器如何响应请求。
  - [模型](/Concepts/Model.md): 定义一个关于 datasource juggler 的对象。 `@loopback/repository` 模块 提供用于添加原数据的特殊装饰 用于 TypeScript/JavaScript 类，用于 DataSource Juggler 的传统实现。(? 不是很顺畅 TODO: 改进), `@loopback/repository-json-schema` 模块 使用装饰元组创建对应的 JSON 模型。
  - [存储](/Concepts/Repositories.md): 表示 DataSource 中的数据集合的服务类型。
  - [修饰](/Concepts/Decorators.md): 用于元数据注释或者修改声明以及其成员的模式。
  - 组件: 捆绑一个或者多个 Loopback 扩展软件包
    - 查看[使用组件](/Using-components/init.md) 和 [创建组件](/Creating-components/init.md) 获得更多详情
  
  