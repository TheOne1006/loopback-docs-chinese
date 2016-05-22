### Access control concepts (访问控制的概念)

Loopback 的 访问控制系统是围绕几个核心概念.  

| Term(控制项目) | Description(描述) | Responsibility(职能)  | Example(举例) |
| ------------- | ---------------- | -------------------- | ------------- |
| Principal(主要) | 一个可以被识别或验证的个体 | 代表一个需要请求受保护资源的个体 | <ul><li> User 用户 </li> <li>  Application 应用  </li> <li> Role 角色（Role本身也是个principle) </li> </ul>|
| ACL           | 访问控制表 | 定义了一个pricipal是否能对一个资源进行某种操作 | <ul><li> 不允许 everyone 访问项目资源 </li> <li> 允许 'admin' 角色执行项目资源上的find()方法 </li> </ul>|
| RoleMapping   | 赋予 Principle 相关的 Roles   | 静态地为Priciplals分配角色  |  <ul><li>为 id 是 1的用户赋予 role1   </li> <li> 将 'admin' role 赋予 role 1 </li> </ul>|
| Role         | 一组有着相同权限的Pricipals | 主要为了将Priciple分组后赋予相同的权限  | 动态的role:<ul><li> $everyone (每个用户都有的Role)</li> <li>$unauthenticated (未认证的用户)</li> <li>$owner (一个资源的拥有者)</li> </ul>   静态的 role:<ul><li>admin (一个实现定义好的管理员) </li></ul>|
