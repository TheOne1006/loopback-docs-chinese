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

#### Client(客户端)

应用程序使代表资源所有者和其授权的受保护资源的请求,
术语“(client)客户机”并不暗示任何特定的实现特性(例如，应用程序是否执行服务器，台式计算机，或其他设备上)  

在 Loopback 中客户也称为应用程序或客户端应用程序.
它是由应用程序模型或应用程序的子模型支持,每个应用程序都有一个ID(又称为 appId)还有一个其他输入如 name,
客户端首选需要注册到Loopback 中.  

#### Authorization server(验证服务器)

认证服务器主要负责验证 资源所有者 后,发放 access Token 到 客户端.  

服务器通常包括HTTP endpoints 的列表的：


- Authorization endpoint(授权端点): 资源所有者通过用户代理获得授权,通过重定向用以客户端使用
- Token endpoint(令牌端点): 让客户端通过 授权码换取一个 access token
- Decision endpoint(角色端点): 由资源所有者来允许/拒绝客户端的访问请求权限
- Login endpoint(登录端点): 由资源所有者用于自身与验证系统进行身份验证。

#### Resource server(资源服务器)

资源服务器负责验证 access tokens, 并决定如果客户有权限调用受保护资源的API。  

资源服务器配置了一个 token 中间件验证请求访问的 access token.确立客户端和资源拥有者和控制者的身份.
如果通过则可以对请求进行到API逻辑.  

请注意用户代理(浏览器)可以参与一些的相互作用,例如: 授权服务器利用HTTP重定向回调与授权码, 或者错误信息处理.
资源拥有者还使用用户代理登录或批准从客户端应用程序的权限申请.

### Metadata models(元数据模型)

元数据是OAuth 2.0的关键,StrongLoop API 网关使用 Loopback models 提供管理和持久化 OAuth 的元数据,
包含:  

- User(用户): 代表资源所有者
- OAuth Client Application(OAuth 客户端应用): 代表之前注册的客户端应用.
- OAuth Access Token(OAuth 访问令牌): 管理元数据属性访问令牌，包括范围，TTL，用户ID和APPID。
- OAuth Authorization Code(OAuth 授权码):管理元数据属性授权码，包括范围，TTL，用户ID和APPID。
- OAuth Permission(OAuth 权限): 由资源拥有者授予客户的管理权限

![oauth2-metadata-models](/images/oauth2-metadata-models.png)

### Pre-authorized permissions(预授权) ???

对于如客户端凭据或JWT断言一定的OAuth 2.0流程, 要求能声称它是代表一个资源所有者无需用户登录.预先授权的权限都需要这种流程.预授权由备份  OAuthPermission模型，它与一组作用域（权限）的连接的用户ID（资源所有者）和应用程序ID（客户端应用程序）
