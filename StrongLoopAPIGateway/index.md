# StrongLoop API网关

> !**Important**: StrongLoop网关废弃于2016年4月22日.
    StrongLoop Gateway 现在属于  [IBM API Connect](https://developer.ibm.com/apiconnect)的一部分,
>> 这是一个为内部部署和云环境解决了API生命周期的所有方面的完整解决方案.它提供了全面的功能来 创建，运行，管理，保护和monetize APIs (??)和微服务.提供了较好的集成用户体验,它能够快速部署和API简化的管理。  

>> 该StrongLoop网关API连接被称为微网关,实际上是一个 __代理__ , securing(保障) 和转发请求到后端.API连接微网关使用StrongLoop技术和一系列中间件组件的创建,详情查看: <https://developer.ibm.com/apiconnect>


- 简介
    - 请求/响应管道
    - API请求典型流程
    - LoopBack 组件 and Express 中间件
    - 策略
- OAuth 2.0
    - 概念
    - 元数据模型
    - 预授权权限

### Overview(简介)

StrongLoop API网关外部化，保证和管理的API,  
这种行为API消费者（客户端）中介于 后端API提供商（API服务器）之间.  


StrongLoop API网关的  功能，总结于下表中。

| 功能      | API 网关                            |
| -------- | ----------------------------------- |
| 安全      | 行为同时提供委托和供应者, 认证, 授权 ,审计（AAA）??? 来源企业作为第一个拦截内确定身份 |
| 基础设施服务质量 | 执行由客户需要，如分页，节流，缓存，交货保证，防火墙等基础设施级别的API函数的消耗 |
| 监测和报告 | 仪器的API实现通过API的监控服务级别协议（SLA），并注入元数据的API使用率，健康，和其他指标的报告。|
| 聚合      | 从构图细粒微API的粗粒度的API（混搭）履行通过动态调用和建设的具体业务情况下的操作。 |
| 调解与转型 | 协议之间介导和有固定的客户端和/或用于消费的特定要求变换的API有效载荷（包括标题和正文）的部分。|


下图说明了API网关架构:  

![api-gateway](/images/api-gateway.png)

#### 请求/响应管道

网关创建一个关于API请求和响应的层的“管道”。管道的层对应的处理的API请求，并编排和构建API响应的不同阶段。  

![loopback-pipeline](/images/loopback-pipeline.png)

API网关“管道”有四个层次：

1. 传输层(Transport layer): 监听传入的消息 and un-marshal them into request objects。例如，LoopBack 使用 Express 来接受HTTP请求，并产生HTTP响应。
2. 远程处理层(Remoting layer): 解析请求对象 到 Node.js对象,然后作为接收器和方法被调用。
3. 模型层(Model layer): 委派数据访问请求到连接器的实现
4. 连接层(Connector layer): 调度CRUD或其他方法到数据库驱动程序调用到后端系统


#### API请求的典型流程

1. 客户端应用程序从 OAuth 2.0 授权服务器 获取一个 access token
2. 客户端应用程序发送 access token 的请求到支持OAuth 2.0 API的资源服务器。
3. 资源服务器验证访问的 access token (包括过期，范围，客户端ID和资源拥有者),用以允许或拒绝访问. 如果拒绝会返回一个失败响应信息到 客户端,并且终止流程
4. Metrics中间件捕获API的使用和更新指标(??)
5. 限速中间件检查 API 请求检查速率限制基于各种 key,如果有一个超出了限制,返回一个失败响应信息到 客户端,并且终止流程
6. 代理中间件会查找路由配置, 以决定请求应该在本地处理还是转发到远程API服务器.
7. API服务器处理该请求，并产生一个响应.
8. 响应由代理中间件接收,然后送回原来的客户端应用程序.

#### LoopBack 组件 and Express 中间件

StrongLoop API 可以动态的指定 Loopback 的组件 包括 Express 中间件.  
因为你可以封装 Express 中间件,在 Loopback 的组件里,
您可以利用 Express中间件 丰富的生态资源系统在管道的协议层.

StrongLoop API网关可以:  

1. __使用OAuth凭据进行身份验证API网关__: 通过身份涵盖了AAA(??), 认证 以及 API endpoint的 授权.
2. __生成 授权(token)__
3. 捕捉调用,创建API调用终点条件
4. 测量每调用间隔（例如：每小时5000请求）.如果调用的次数超过该规则,会阻塞请求,或者其他处理.
5. 代理请求到一个 Loopback 实例中


您也可以声明并指定通过Loopback链在一起上述功能的政策.您可以在管道内的任何层指定策略.

#### 策略

StrongLoop API网关 将会评估条件和管道的每个层产生的数据，并在单个请求范围，资源端点或甚至全球用户，机器或其它维度并将其添加到API上下文.信息被连续地和有条件地进行评价到产生行为.  
一般来说,在更深的层会有越多的信息添加到API上下文和进一步向下请求/响应链.  

Loopback 的策略 描述和管理 流水线执行的过程,  
策略是在相同的水平的范围内的第一类对象, Loopback 运行时持续评估每一层的管道政策的API请求处理的一部分.  
策略有三个主要组件：范围(scope)，约束(constraint)和动作(action)。  

Loopback策略实施禁止请求规则, 例如:  

- 强制限速(Enforce rate limiting)
- 实施重试失败或禁止的请求(Implement retrying a failed or blocked request)
- 数据验证(Validate data)
- 收集度量(Collect metrics)
- 检查的ACL(Check ACLs)
