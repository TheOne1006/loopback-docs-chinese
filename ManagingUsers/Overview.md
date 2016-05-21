## Managing users

Loopback 内置的 User 模块提供了基本的用户管理功能，例如:  

- 注册, 通过 email 认证
- 登录登出
- 创建 token
- 密码重置

> **!important**: 自定义的模型不得以 `User`, `Application` 等内置 Model 相同的名字命名.


### Creating and authenticating users(创建与认证用户)

基本过程创建和验证用户身份是:  

1. 注册一个新用户使用 User.create(), 从PersistedModel对象继承的方法.
2. 用户登录使用  User.login(), 获得一个 access token
3. 随后的api 调用使用这个 access token , 提供HTTP头或查询参数的REST API调用的访问令牌,详细信息 [Making authenticated requests with access tokens](https://docs.strongloop.com/display/LB/Making+authenticated+requests#Makingauthenticatedrequests-Makingauthenticatedrequestswithaccesstokens)

> 性能提示
    要登录和用户创建过程中提高性能,安装原生的  bcrypt 包:
    `$ npm install --save bcrypt`

### Understanding the built-in User model(了解内置User Model)

默认 Loopback 应用的内置 User Model 来自 user.json(在loopback freamework)里.

#### Default access controls(默认访问控制)

内置的用户模型 具有以下ACL:  
查看: `node_modules/loopback/common/models/user.json`  




- - -
