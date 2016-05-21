### Partitioning users with realms (用户区分)

默认情况下, User Model 管理所有用户在一个全局命名空间.
它不隔离不同的应用程序.  
在某些情况下, 需要在让不同的用户拥有一些不同的命名空间.

Loopback 支持领域划分:  

- Users 和 applications 属于 一个全局的领域(或者说是没有领域).
- 将用户和 applications 规划到不同的领域, 一个用户 和 一个 application __只能属于一个领域__ .每个领域可以拥有多个用户,多个 application.
- 每个 application 是一个独特的境界, 每个用户都属于一个 application (通过 realm)


每个应用程序或用户实例,都有一个唯一的 ID 访问境界, 当一个应用程序/用户登录时，它可被分配给一个境界.
在 `User.login()` 方法里:  

- 赋予realm(领域)属性 从 user credential(凭据)
- 允许从 用户名/电子邮件 的前缀中提取的 realm(领域) 信息

设置 User model options 属性 的控制领域:  

- realmRequired (Boolean): true | false (default)
- realmDelimiter (string): 如果配置, email 或者 username 就会加上前缀:
     `<realm><realmDelimiter><username or email>`

demo 用户属性设置:  

```json
// server/model-config.json
"User": {
  "dataSource": "db",
  "options": {
    "realmRequired": true,
    "realmDelimiter": ":"
  }
}
```


当 realms(领域)开启后, 在使用 `User.create()` 时,需要指定 `realm` 属性.  

例如:  

```js

User.create({
  realm: 'myRealm',
  username: 'john',
  email: 'john@sample.com',
  password: 'my-password'
}, callback);
```

用户登录的时候 `User.login()` 也需要realm属性  

```js
User.login({
  realm: 'myRealm',
  username: 'john',
  password: 'my-password'
}, callback);

// 或者用这种方式登录
User.login({
  username: 'myRealm:john',
  password: 'my-password'
}, callback);
```





- - -
