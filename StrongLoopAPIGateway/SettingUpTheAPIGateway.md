## Setting up the API Gateway(设置API网关)

- 简介
- 一般流程
- 案例
    - Web 应用(API 消费者)
    - API Gateway (api 网关)
    - LoopBack 应用
    - 执行 demo


### 简介

该StrongLoop API网关实际也是LoopBack的应用程序, 你可以将 网关模块合并到任何 LoopBack API 服务实例的进程中,也可以单独运行  

一个典型的API 网关部署如下面的流程图,以下组件参与:  

- API Gateway(API 网关)
    - OAuth 2.0 authorization server(OAuth 授权服务器)
    - OAuth 2.0 resource server( OAuth 资源服务器)
    - Loopback 的API 网关策略
- Client application(客户端应用)
    - Backend - web server(后端 - Web服务器)
    - Backend - API server(后端 - API 服务器)
    - Frontend - Web/mobile client (前端 - web 客户端/mobile)

![网关部署流程](/images/oauth2-server-client.png)

客户端应用的API调用到被保护，并且通过API网关代理API服务器.具体步骤如下:

1. 客户端应用呈现从 web 服务端提供的用户服务界面.
2. 客户端应用从 OAuth 2.0授权服务器 请求获得到 一个授权码 / 访问令牌
3. OAuth 2.0授权服务器将浏览器重定向到回调端点并接收访问令牌或授权码.对于授权码,web 服务器将调用 OAuth 服务器的调用接口 换取访问令牌
4. 客户端应用通过 API 带着 访问令牌调用API网关. OAuth 2.0 资源服务器验证 访问令牌 或者拒绝无效.
5. 请求到策略列表
6. 代理组件调度API请求到客户端应用程序的API服务器

### 一般流程

请按照以下步骤整合与API网关的应用程序：  

1. 检出 strong-gateway-demo
2. 更新 datasources.json
3. 编辑 model-config.json 更改数据源
4. 修改 `strong-gateway/server/scripts/setup-db.js` 然后执行


### 案例

[strong-gateway-demo](https://github.com/strongloop/strong-gateway-demo)展示了一个API消费者之间的相互作用 API网关 以及API服务器, 该示例包含三个不同的应用程序：

- [demo-web-server](https://github.com/strongloop/strong-gateway-demo/blob/master/README.md#demo-web-server) - API 消费者
- [strong-gateway](https://github.com/strongloop/strong-gateway-demo/blob/master/README.md#strong-gateway) - API 网关
- [demo-api-server](https://github.com/strongloop/strong-gateway-demo/blob/master/README.md#demo-api-server) - API 服务器

下图说明了该架构:

![demo](/images/oauth2-server-client.png)

#### Web application (API consumer API 消费者)

该案例是一个简易的 web 服务用于访问REST APIs 通过 API 网关,访问是通过下列流程:  

- Implicit flow 隐式流程(又名作为客户端流程)
- Authorization code flow(授权码流程)
- Client credentials flow(客户端凭证流程)
- Resource password owner credentials flow(资源密码所有者凭证流程)

#### API Gateway (API 网关)

该案例包含一个 StrongLoop API 网关实例  

#### Loopback 应用

该案例是一个带有 REST API 的 Loopback 引用,它有一个 Model 并且有个属性: content  

#### 执行demo

随着其他步骤然后运行demo:  

1. 在项目的根目录并输入以下命令来启动, 用来启动服务, 监听端口从 3000 - 3005
    - `npm start`
2. 访问 <http://localhost:3000> 浏览器将被重定向到 <https://localhost:3001>, 你将看到一些链接
3. 通过选择启动 隐式流程(又名作为客户端流程), 你会看到一个弹出，询问您是否要被重定向到指定的URL(注意请求字段是否匹配 于API 网关), 选择确定.
4. 在这一点,的浏览器将被重定向到网关的认证页面(注意 url 现在<https://localhost:3005>  ，而不是<https://localhost:3001>), 登录使用提供的凭据: 单击 提交
5. 一旦登出成功,注意网址更改为 <https://localhost:3005/oauth/authorize?client_id...> ,这个网关服务将会是否发放权限到 demo-app ,点击允许批准授予。你将会被重定向到 <https://localhost:3001/implicit-flow.html>
6. 重定向后, 注意url 变为 <https://localhost:3001/implicit-flow.html#access_token=...>, 这里要注意的关键是 __嵌入URL的 access token 访问令牌__ , 这是用来由API网关保护接入资源, 你也应该看到在页面上嵌入在各自的URL访问令牌两个环节。
7. 第一个链接, 访问的OAuth 2.0保护的资源是由托管API网关保护本地服务器上的资源的链接, 点击链接，你会看到该URL将您重定向到<localhost:3001/protected/protected-apis.html...> 本案的情况是API网关代理您的申请回托管在本地服务器上的资源, 详情查看 <>
8. 点击浏览器的后退按钮返回页面列出的资源,点击第二个链接 调用 `/api/notes`
9. 你将看到 notes 结果
10. 在这种情况下, demo-api-server 提供资源于 __localhost:3002__ , 我们对API网关请求被代理到  演示API服务器  并把结果发回的JSON(注意URL是  <https://localhost:3005/api/notes>  ，而不是  <https://localhost:3002/api/notes>)  ??
11. 这就是它的隐式流程,点击回退按钮，然后点击 首页的链接,尝试一些网页上列出的其他流程,（授权码，客户端证书和资源所有者密码凭据）模式,每个流程都有自己的特点. 详细查看 [Developer's Guide](https://docs.strongloop.com/display/LGW/Developer%27s+Guide)




















- - -
