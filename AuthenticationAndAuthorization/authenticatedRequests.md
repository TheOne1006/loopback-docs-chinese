### Making authenticated requests(请求身份验证)

- 通过 access tokens 进行身份验证
- 在REST URL中使用当前用户ID
- 删除 access token

应用中,创建和验证用户的基础流程过程是:  

1. 注册用户通过 `User.register()`, 继承 `PersistedModel` 对象
2. 使用 `User.login()`, 让用户在客户端获得 access token,
3. 使用 access token 调用API


#### Making authenticated requests with access tokens (通过 access tokens 进行身份验证)

在用户登录之后,系统会根据用户,创建一个 新的 access token,这个 access token 在之后的api使用.  

demo 使用 curl:
```bash
# 设置变量
ACCESS_TOKEN=6Nb2ti5QEXIoDBS5FQGWIz4poRFiBCMMYJbYXSGHWuulOuy0GTEuGx2VCEVvbpBK

# 方法一: 设置报文 header Authorization 字段
curl -X GET -H "Authorization: $ACCESS_TOKEN" \
http://localhost:3000/api/widgets

# 方法二,通过参数传递
curl -X GET http://localhost:3000/api/widgets?access_token=$ACCESS_TOKEN
```

使用Cookie进行身份验证，添加以下server.js（在 boot 之前）:
```js
app.use(loopback.token({ model: app.models.accessToken }));
```

#### Using current user id as a literal in URLs for REST (在REST URL中使用当前用户ID)

允许rest api中使用自己的 userId, 需要配置, token 的中间件设置 `currentUserLiteral` 选项.  

```js
app.use(loopback.token({ model: app.models.accessToken, currentUserLiteral: 'me' }));
```

使用 _currentUserLiteral_ 标识案例
```bash
curl -X GET http://localhost:3000/api/users/me/orders?access_token=$ACCESS_TOKEN
```

#### Deleting access tokens(删除 access token)

用户登出时,需要将用户的 access token 销毁, 用户的访问将受到限制,但不会影响其他用户的访问:  

demo:  
```js

var USER_ID = 1;
var ACCESS_TOKEN = '6Nb2ti5QEXIoDBS5FQGWIz4poRFiBCMMYJbYXSGHWuulOuy0GTEuGx2VCEVvbpBK';
// remove just the token
var token = new AccessToken({id: ACCESS_TOKEN});
token.destroy();
// remove all user tokens
AccessToken.destroyAll({
  where: {userId: USER_ID}
});
```
