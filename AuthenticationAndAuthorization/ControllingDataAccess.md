### Controlling data access(数据访问控制)

1. 指定用户角色
    - 用户 access 类型
2. 定义访问控制
3. 使用该ACL构建器
    - Example
4. Applying access control rules
    - ACL rule precedence
5. Debugging


#### Specifying user roles(指定用户角色)

第一步确定需要什么样的权限是 app 需要的, 大多数限制在于 区分 登录认证用户/非登录用户, 以及 是否有管理员权限. 应需要对额外的角色赋予一定的权限.  

##### User access types (用户 access 类型)

loopback 内置的 User model 拥有响应的REST API ,它们继承  PersistedModel 的 CURL 方法.  
每一个 User model 的 CURL 方法, 都会映射到 READ 或者 WRITE 方法.  
具体如下:  

__READ__:  

1. exists() : 确定用户是否存在布尔方法
2. findById() : 通过 Id 查找用户是否存在
3. find(): 通过 匹配 查找所有 User
4. findOne(): 查找符合指定条件的单个用户实例
5. count(): 统计符合条件的用户数量

__WRITE__:  

1. create(): 创建一个新用户
2. updateAttributes (update) : 更新一个用户的记录
3. upsert (update or insert) : 更新/创建一个用户
4. destroyById (equivalent to removeById or deleteById) : 通过id 删除指定记录

__EXECUTE__:

其他的方法,默认的访问类型是 __EXECUTE__  


#### Defining access control(定义访问控制)

使用 ACL generator 可以创建访问控制,不过之前需要明确的了解 配置选项  

举个例子如  [loopback-example-access-control](https://github.com/strongloop/loopback-example-access-control)中的设置:  

- Guest - Guest
    - Role = $everyone, $unauthenticated
    - 只能够访问"项目列表"的方法
- John - Project owner (项目所有者)
    - Role = $everyone, $authenticated, teamMember, $owner
    - 能够访问所有 方法, 除了"访问所有项目详情"
- Jane - Project team member (项目团队成员)
    - Role = $everyone, $authenticated, teamMember
    - 能够访问所有 方法, 除了 "访问所有项目详情" 和 "撤销"
- Bob - Administator (管理员)
    - Role = $everyone, $authenticated, admin
    - 能够访问所有 方法, 除了 "撤销"


#### Using the ACL generator to define access control(使用该ACL构建器)

最简单的方法是使用 `$slc loopback:acl` 构建权限

#### Applying access control rules(应用访问规则)

所有请求会被映射到具有三个属性的对象:  

1. model - 目标模型名称 ,如 'User'
2. property - 目标方法名, 如 'find', 也可以使用数组, 让他们拥有相同的规则
3. accessType - 类型, 有 'EXECUTE', 'READ', 'WRITE'

ACL规则被描述为对象的数组:  

1. model
2. property
3. accessType
4. principalType
    1. USER
    2. APP
    3. ROLE
        1. custom roles
        2. $owner
        3. $authenticated
        4. $unauthenticated
        5. $everyone
5. permission
    1. DENY
    2. ALLOW


多条规则进行匹配每一个规则的要求的具体分类, 具体是由上述属性的等级秩序检查，对每个ACL规则访问请求计算.  

> 在每个级别，匹配得出三点:  

- 3: exact match            精确匹配
- 2: wildcard match ('\*')  通配符
- -1: no match              未匹配

得分高的规则会接管低规则,


- - -
