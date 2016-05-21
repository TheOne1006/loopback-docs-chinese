## LoopBack core concepts(核心概念)

> __Important__: 先阅读此文,了解 Loopback 是如何运行的

- Models(数据模型)
- Application logic(业务逻辑)
- Data sources and connectors (数据源和连接器)
- LoopBack components (Loopback 组件)
- development tool(开发工具)
- Examples(案例)

### Models(数据模型)

- 内建的数据模型(Built-in models)
- 自定义数据模型(Custom models)
- 数据模型关系(Model relations)
- 数据模型的CRUD操作(Model create, retrieve, update, and delete operations)

LoopBack 的核心就是数据模型。数据模型用于描述后端的数据源，如数据库或其他RSET, SOAP等后端服务。从使用者的角度来看，LoopBack的数据模型可以是一个JS对象或者是一组REST的API。  



__所有LoopBack的数据模型都自带了一组REST API用于CRUD操作。__  

所有的数据模型最终都继承自一个[基础模型对象]。
这个[基础模型对象]具有让所有继承自它的数据模型具有添加 [hooks] 和 [数据验证] 的能力。
数据模型间的继承关系如下图所示。
当你将数据模型连接到一个持久层的数据源时，那么这个数据模型将成为一个具备CRUD操作的已连接模型。所有LoopBack的内建的数据模型同样继承自[基础模型对象]。

![Model inheritance](/images/Model-inheritance.png)


#### 内建的数据模型(Built-in models)

所有的LoopBack应用程序都会自带一组内建的数据模型，例如：User, Role 和 Application等。因此开发人员无需从头定义这些模型，而可以继承自这些内建的数据模型。

#### 自定义数据模型(Custom models)

你也可以为你的应用创建自定义数据模型。 当然，你的自定义模型也完全可以扩展内建的数据模型，从而复用内建的 User, Application 等模型的功能。

#### 数据模型关系(Model relations)

LoopBack 可以让你方便地使用BelongsTo, HasMany 和 HasAndBelongsToMany 来表述数据模型间的关系。

#### 数据模型的CRUD操作(Model create, retrieve, update, and delete operations)

当你将数据模型连接到一个持久层的数据源（例如数据库或外部的REST API）时，那么这个数据模型将成为一个已连接模型。已连接模型具备完整的继承自PersistedModel的CRUD操作能力。

| 操作 | REST | LoopBack model method (Node API)* | 对应的SQL操作 |
|-----|-------| --------------------------------- | ----------- |
| Create | PUT /modelName   POST /modelName |  create( )\* | INSERT |
| Read (Retrieve) | GET /modelName?filter=... | find( )\*   | SELECT |
| Update (Modify) | POST /modelName   PUT /modelName | updateAll( )\* | UPDATE |
| Delete (Destroy) | DELETE /modelName/modelID | destroyAll( )\* | DELETE |

### Application logic(业务逻辑)

你可以用以下三种方法为一个LoopBack应用添加业务逻辑:  

- __在数据模型中添加业务逻辑__。在这个场景下，你可以 remote methods (即自定义的绑定到数据模型REST API上的方法), remote hooks (在调用模型的remote methods时被触发) 和 model hooks (在调用模型的CRUD操作时被触发) 上添加业务逻辑
- 在 __bootScripts__ 上添加应用启动时的业务逻辑。
- 通过 __中间件__ 为应用添加业务逻辑。类似于传统的Express中间件.


#### Middleware phases (阶段型中间层)

当应用响应一个HTTP的REST请求时，中间层逻辑会被依次触发。  
因为LoopBack本身是基于Express的，所以LoopBack的中间件也类似于Express 中间件。  
不过，LoopBack的中间件引入的 __阶段 (Phase)__ 的概念。  
通过将中间件定义在不同的阶段，开发人员可以更明确地定义中间件的触发顺序，
从而避免在标准的Express中间件触发顺序的问题。


### Data sources and connectors (数据源和连接器)

LoopBack将后台的数据服务(例如：数据库，REST和SOAP API，以及其他存储服务) 统称为数据源。  
数据源通过连接器直接操作数据库和其他后台服务。
应用程序不会直接使用连接器，而是通过 DataSource and PersistedModel API方法来访问后台服务。

![Data sources and connectors](/images/Data-sources-and-connectors.png)





[基础模型对象]: https://docs.strongloop.com/display/zh/Basic+model+object        "Basic+model+object"
[hooks]: http://docs.strongloop.com/display/LB/Model+hooks        "Model hooks"
[数据验证]: https://docs.strongloop.com/display/zh/Validating+model+data        "Validating model data"
[Push notifications]: http://docs.strongloop.com/display/LB/Push+notifications "Push notifications"
[Storage service]: http://docs.strongloop.com/display/LB/Storage+service "Storage service"
[Third-party login]: http://docs.strongloop.com/display/LB/Third-party+login "Third-party login"
[Synchronization]: http://docs.strongloop.com/display/LB/Synchronization "Synchronization"
[OAuth 2.0]: http://docs.strongloop.com/display/LB/OAuth+2.0 "OAuth 2.0"
