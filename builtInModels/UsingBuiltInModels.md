## Using built-in models(使用内置Model)

- 简介
- Application model
- User model
- Access control models
    - ACL model
- Email model
    - 发送 email 信息
    - 确定 email 地址

### 简介

Loopback 提供了常见的内置 Model:  

- Application model : 包含有它自己的身份和相关的配置和端口 Loopback 服务器的客户端应用程序的元数据
- User model : 应用的用户注册/登录,或则针对第三方应用
- Access control models : ACL, AccessToken, Scope, Role, 和 RoleMapping models ,控制 应用, 访问请求 和 方法.
- Email model : 发送邮件到应用的用户,使用 STMP 或者第三方 服务.

内置 model 基本上都继承 PersistedModel,(除了 Email), 它们会自动获得一套 增删改查 操作.  

> 默认情况下,只有 User 暴露出 REST 方法,

### Application model

使用 Application model 管理客户端应用以及组织它们,

### User model

User model 代表应用的用户或者 api,

### Access control models

访问控制,它包括:  

- ACL
- AccessToken
- Scope
- Role
- RoleMapping

#### ACL model

一个  ACL model 主要负责连接受保护资源, 该系统授予的权限的主体(用户或应用程序，其可被分成角色)  

- Protected resources(受保护的资源): 模型数据和操作 (Model/属性/方法/关系)
- 是否一个给定的客户端应用程序或用户 允许访问（读，写或执行）的受保护的资源

创建一个新的 ACL 实例:  

如 `server/boot/script.js`:  
```js

ACL.create( {principalType: ACL.USER,
             principalId: 'u001',
             model: 'User',
             property: ACL.ALL,
             accessType: ACL.ALL,
             permission: ACL.ALLOW},
             function (err, acl) { ACL.create( {principalType: ACL.USER,
                                                principalId: 'u001',
                                                model: 'User',
                                                property: ACL.ALL,
                                                accessType: ACL.READ,
                                                permission: ACL.DENY},
                                                function (err, acl) { }
                                                })
                                 }
             })
```

#### Email model

通过添加设置电子邮件数据源/server/datasources.json，如以下的（例如）：  

`server/datasources.json`:  

```json
{
    "myEmailDataSource": {
       "connector": "mail",
       "transports": [{
         "type": "smtp",
         "host": "smtp.private.com",
         "secure": false,
         "port": 587,
         "tls": {
           "rejectUnauthorized": false
         },
         "auth": {
           "user": "me@private.com",
           "pass": "password"
         }
       }]
    }
}
```

然后，引用数据源中/server/model-config.json（例如）如下：  

`server/model-config.json`:  

```json
{
  "Email": {
    "dataSource": "myEmailDataSource",
  }
}
```

#### Send email messages

下面的例子演示了如何从一个应用程序发送电子邮件。下面的代码添加到该文件 /model 目录：  

`server/models/model.js`:  

```js

module.exports = function(MyModel) {
  // send an email
  MyModel.sendEmail = function(cb) {
    MyModel.app.models.Email.send({
      to: 'foo@bar.com',
      from: 'you@gmail.com',
      subject: 'my subject',
      text: 'my text',
      html: 'my <em>html</em>'
    }, function(err, mail) {
      console.log('email sent!');
      cb(err);
    });
  }
};
```













- - -
