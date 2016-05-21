### Registering users(用户注册)

Loopback 的内置User Model,提供了注册,确认 Email 地址.  
同时通过 `loopback-component-passport` 模块能够整合 google, Facebook ,github 等第三方登录.

- 通过内置 User Model 注册用户
    - 创建一个新用户
    - 添加注册限制
    - 验证 email 地址
- 使用第三方注册

#### 创建一个新用户

创建一个用户（注册的使用者）通过添加模型实例，以同样的方式作为任何其他模式;  
电子邮件地址和密码是唯一需要的属性。

> 可以通过 boot script 注册默认用户

```js
module.exports = function(app) {
  var User = app.models.User;
  User.create({email: 'foo@bar.com', password: 'bar'}, function(err, user) {
    console.log(user);
  });
```

> 可以通过, POST 方式创建一个新用户

```bash
curl -X POST -H "Content-Type:application/json" \
-d '{"email": "me@domain.com", "password": "secret"}' \
http://localhost:3000/api/users
```

> 可以通过 explorer

通过 explorer 提供的图形管理工具添加用户

#### 添加注册限制

通常情况需要在注册过程中添加自己的逻辑,比如 检测 用户名, email 是否重复.  
一个好方法是添加方法为 beforeRemote 用户对象上的钩子.

#### 验证 email 地址

通常情况,应用需要在用户登录之前,先认证邮箱.  
应用将发送一封电子邮件包含的链接以验证其地址的用户身份,
一旦用户点击链接之后,通过认证.  

如何启用这以限制?  
需要在 User 的 配置信息中将 `options.emailVerificationRequired` 属性设置为 `true`.

在下面的案例中, 添加的 user.afterRemote('create', function) 添加发送方法  

```js
var config = require('../../server/config.json');
var path = require('path');

module.exports = function(user) {
  // 在用户成功完成注册的方法后, 发送邮件
  user.afterRemote('create', function(context, userInstance, next) {
    console.log('> user.afterRemote triggered');

    var options = {
      type: 'email',
      to: userInstance.email,
      from: 'noreply@loopback.com',
      subject: 'Thanks for registering.',
      template: path.resolve(__dirname, '../../server/views/verify.ejs'),
      redirect: '/verified',
      user: user
    };

    // 验证发放, 发送邮件信息
    userInstance.verify(options, function(err, response, next) {
      if (err) return next(err);

      console.log('> verification email sent:', response);

      context.res.render('response', {
        title: '注册成功',
        content: '请先完成邮箱认证再登陆',
        redirectTo: '/',
        redirectToLinkText: 'Log in'
      });
    });
  });
```

提供 verify.ejs 文件,案例为

```html
This is the html version of your email.
<strong><%= text %></strong>
```

#### 使用第三方注册

使用 loopback-component-passport 组件, 详细查看 [Third-party login using Passport](https://docs.strongloop.com/display/LB/Third-party+login+using+Passport)
