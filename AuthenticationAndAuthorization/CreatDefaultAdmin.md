### Creating a default admin user(创建默认 Admin)

Loopback 不会默认创建管理员账号, 然而应用程序,你可以自定义一个,
像一个 [loopback-example-access-control](https://github.com/strongloop/loopback-example-access-control) 里的  `server/boot/sample-models.js`.

步骤:  
1. 创建多个用户，连同其他模型的实例  
2. 定义模型之间的关系。
3. 定义管理员角色
4. 添加一个角色映射到指定用户的管理角色

因为在 `server/boot`, 当应用程序启动时，它就会被执行, 所以admin用户会一直存在，一旦应用程序初始化.  

```js
/**
 * 创建多个用户，连同其他模型的实例
 */
User.create([
    {username: 'John', email: 'john@doe.com', password: 'opensesame'},
    {username: 'Jane', email: 'jane@doe.com', password: 'opensesame'},
    {username: 'Bob', email: 'bob@projects.com', password: 'opensesame'}
], function(err, users) {
    if (err) return debug('%j', err);
    ...
    // 创建项目,指定项目业主和项目团队成员
    ...
    /**
     * 定义管理员角色
     */
    Role.create({
      name: 'admin'
    }, function(err, role) {
      if (err) return debug(err);
      debug(role);

      /**
       * 添加一个角色映射到指定用户的管理角色
       */
      role.principals.create({
        principalType: RoleMapping.USER,
        principalId: users[2].id
      }, function(err, principal) {
        if (err) return debug(err);
        debug(principal);
      });
    });
  });
};
```


定义模型之间的关系。 `/common/models/project.json`  

```json
    {
      "accessType": "READ",
      "principalType": "ROLE",
      "principalId": "admin",
      "permission": "ALLOW",
      "property": "find"
    },
```
