### Defining and using roles(定义和使用 角色)

- 静态角色
- 动态角色

Loopback 允许使用定义 静态 和 动态角色  
区别:
- 静态角色被存储在一个数据源中,同时映射到用户上
- 没有确定在哪里赋予用户角色

#### Static roles

下面代码段,展示如何 定义一个新的静态角色 并 分配用户给该角色

```js
// 创建用户
User.create([
    {username: 'John', email: 'john@doe.com', password: 'opensesame'},
    {username: 'Jane', email: 'jane@doe.com', password: 'opensesame'},
    {username: 'Bob', email: 'bob@projects.com', password: 'opensesame'}
  ], function(err, users) {
    if (err) return cb(err);

    // 创建角色, 设置 role 的 name 属性
    Role.create({
      name: 'admin'
    }, function(err, role) {
      if (err) cb(err);

      // 绑定 Bob 成为 role 角色
      role.principals.create({
        principalType: RoleMapping.USER,
        principalId: users[2].id
      }, function(err, principal) {
        cb(err);
      });
    });
  });
```

然后可以使用访问控制上面定义的角色:  

```json
{
  "accessType": "EXECUTE",
  "principalType": "ROLE",
  "principalId": "admin",
  "permission": "ALLOW",
  "property": "find"
}
```
#### Dynamic roles

有时候,静态角色不够灵活, Loopback 允许定义 _动态属性_ 在运行时.  

Loopback 提供了一下 内置的动态角色:  

| Role object property(角色对象属性) | 字符串值           |  描述     |
| -------------------------------- | ---------------- | --------- |
| Role.OWNER                       | $owner            | 对象所有者 |
| Role.AUTHENTICATED               | $authenticated    | 认证用户  |
| Role.UNAUTHENTICATED             | $unauthenticated  | 未认证用户 |
| Role.EVERYONE                    | $everyone         | 任何用户  |

> 使用 `$owner`时, 目标模型需要有一个关系的 _belongsTo_ 到 User Model(或者其他继承 user 的 model),
    同时 属性疲惫到 目标模型对象实例的一个外键. `$owner` 检查只有 `:id` 的 remote 方法 ?


使用 `Role.registerResolver()` 注册一个 自定义角色,在 引导脚本中, 这个方法需要两个参数:  

1. 第一个参数: 角色名字符串
2. 第二个参数: function 确定主要是在指定的作用, `function(role, context, callback)`

Demo
```js
// 在 bootScript 中
module.exports = function(app) {
  // 获取 Role 的 model
  var Role = app.models.Role;
  // 注册名为 teamMember 的橘色名,并设置逻辑
  // role 新注册的 teamMember 的实例
  // 上下文, 回调函数 cb , cb(err, false) 为失败, 为 cb(null, true) 为通过
  Role.registerResolver('teamMember', function(role, context, cb) {

    /**
     * rejest 认证失败调用方法
     * @param  err
     */
    function reject(err) {
      if(err) {
        return cb(err);
      }
      cb(null, false);
    }

    // 角色只真对 project Model
    if (context.modelName !== 'project') {
      return reject();
    }

    // 只能是登录用户
    var userId = context.accessToken.userId;
    if (!userId) {
      return reject(); // do not allow anonymous users
    }

    // 查找项目id
    context.model.findById(context.modelId, function(err, project) {
      // 没找到项目, 权限失败
      if(err || !project) {
        reject(err);
      }

      // 获取 Team
      var Team = app.models.Team;

      // 查询先关 team 和 user 的关联是否建立
      Team.count({
        ownerId: project.ownerId,
        memberId: userId
      }, function(err, count) {
        if (err) {
          return reject(err);
        }
        cb(null, count > 0); // true = is a team member
      });
    });
  });
};
```

使用以上方法定义角色后,我们可以限制是该项目的团队成员的用户的项目信息的访问.  

```json
{
  "accessType": "READ",
  "principalType": "ROLE",
  "principalId": "teamMember",
  "permission": "ALLOW",
  "property": "findById"
}
```

- - -
