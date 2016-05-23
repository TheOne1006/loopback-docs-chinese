## OAuth 2.0

OAuth 2.0(Open Anthentication) 是一个 开放标准授权, 它授予客户端应用程序安全授权访问受保护的服务器资源代表资源所有者（用户）.Oauth2.0 还定义了资源拥有者授权的第三方访问其服务器资源过程,不予它们共享证书.  

StrongLoop API网关附带实现的OAuth 2.0协议,并提供元数据的持久性和管理一个OAuth 2.0提供者。  
下图提供了有关我们的OAuth 2.0架构一个的图片:

![oauth2-infrastructure](/images/oauth2-infrastructure.png)


### 概念

#### Resource Owner(资源所有者)

能够准许访问受保护资源的实体。当资源的所有者是一个人，它被称为终端用户。  

在 Loopback 中, 资源所有者(Resource Owner) 也被称为 User(用户). 可以是 内建的 User 模型/或者是继承与 User 的其他模型. 每个资源所有者都有一个 Id (引用是称为 UserId) 还有其他属性如 Email, username.  
资源所有者可以通过 LoopBack 获得使用凭证,通常使用 用户名/邮箱和密码 进行身份验证.  
