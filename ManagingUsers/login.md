### Logging in users(用户登陆)

- loopback User Model 登陆
    - 登陆
    - 登出
- [第三方登陆系统](https://docs.strongloop.com/display/APIC/Third-party+login+using+Passport)
- 密码重置



#### 登陆

调用 User.login() 方法实现 登陆/认证, 需要提供一个带有 password 和 email/username 属性的对象,作为函数的第一个参数, 方法返回一个 `access token`  


代码案例:  
```js
/**
 * 登陆方法
 * @param  {object} credentials  username/password or email/password
 * @param  {array/string} include  可选, 设置为'user' 回调的 token, 会带有 user 的信息, array ?
 * @param  {function} callback(err, accessToken){ }
 */
User.login({username: 'foo', password: 'bar'}, [include],function(err, accessToken) {
  console.log(accessToken);
  // {
  // ttl: 1209600,
  // userId: 574078e2fdecae6105b9be3e,
  // created: Sat May 21 2016 23:04:02 GMT+0800 (CST),
  // id: 'xS7Ge8T9WFEV17o0wK3KIqaU3M98QvlIcPKCTkaT0ZzWcdANGF7mKCkE89K18G1M'
  // }
});

myUserModel.login({username: 'foo', password: '123'}, 'user',function(err, accessToken) {
  debug(accessToken);
  // {
  // ttl: 1209600,
  // userId: 574087e1bc38978c0768f27a,
  // created: Sun May 22 2016 00:08:01 GMT+0800 (CST),
  // id: '1wU88tlNMoO3KlyypHSOXmcBiVlSF2QKlC4qOjFqSZN8xsRgpTZrzV4Sg2ZGHbxp',
  // user: {
  //    username: 'foo',
  //    password: '$2a$10$NNeLcFekDP5XGW4Wqa/LM.55twmAZq0daoNpsyzRa9j3iDRsXzqha',
  //    email: 'foo@bar.com',
  //    id: 574087e1bc38978c0768f27a
  //    }
  // }
  cb();
});

```

也可以指定一个ttl (time to live) 属性来指定 accessToken 的过期时间(seconds)

#### 登出

使用 `User.logout()` 方法登出.  

```js
/**
 * 用户登出
 * @param  {string} 用户的accessToken
 */
User.logout(req.accessToken.id, function(err) {
     if (err) return next(err);
     res.redirect('/'); //on successful logout, redirect
});


/**
 * 在服务器端(server side only)
 * 可以使用user的实例
 */
 User.findOne({email: 'foo@bar.com'}, function(err, user) {
   user.logout();
 });
```

#### Login using third-party systems(第三方登陆系统)

使用 第三方 登出插件,可以替代 用户的登陆系统.  

更多详情: [Third-party login using Passport](https://docs.strongloop.com/display/APIC/Third-party+login+using+Passport)


#### Resetting a user's password(重置user密码)

使用 `User.resetPassword()` 方法重置用户密码,此方法创建临时登录一个短暂的访问令牌，使如果忘记了用户更改密码.(机制??)

流程:  

1. 调用`User.resetPassword()`方法
2. 发送一封带有`short-lived access token`的邮件
3. 使用 `updateAttribute()` 更新密码


```js
// 使用邮箱,创建一个 short-lived 的 accessToken
  app.post('/request-password-reset', function(req, res, next) {
    User.resetPassword({
      email: req.body.email
    }, function(err) {
      if (err) return res.status(401).send(err);
      res.render('response', {
        title: 'Password reset requested',
        content: 'Check your email for further instructions',
        redirectTo: '/',
        redirectToLinkText: 'Log in'
      });
    });
  });

  // 发送忘记密码的短信, 带有 accessToken
  user.on('resetPasswordRequest', function(info) {
    var url = 'http://' + config.host + ':' + config.port + '/reset-password';
    var html = 'Click <a href="' + url + '?access_token=' +
        info.accessToken.id + '">here</a> to reset your password';
    //'here' in above html is linked to : 'http://<host:port>/reset-password?access_token=<short-lived/temporary access token>'
    user.app.models.Email.send({
      to: info.email,
      from: info.email,
      subject: 'Password reset',
      html: html
    }, function(err) {
      if (err) return console.log('> error sending password reset email');
      console.log('> sending password reset email to:', info.email);
    });
  });


  // 更新 password
  User.findById(req.accessToken.userId, function(err, user) {
     if (err) return res.sendStatus(404);
     user.updateAttribute('password', req.body.password, function(err, user) {
     if (err) return res.sendStatus(404);
       console.log('> password reset processed successfully');
       res.render('response', {
         title: 'Password reset success',
         content: 'Your password has been reset successfully',
         redirectTo: '/',
         redirectToLinkText: 'Log in'
       });
     });
   });


```
