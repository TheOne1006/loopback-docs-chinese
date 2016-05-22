## Authentication, authorization, and permissions (认证，授权和权限)

- Access control concepts (访问控制的概念)
- General process (一般流程)
- Exposing and hiding models, methods, and endpoints (揭露和隐藏模式，方法和触发点)
    - Hiding methods and REST endpoints (隐藏的方法和REST 触发点)
    - Read-Only endpoints example (只读端点的案例)
    - Hiding endpoints for related models (隐藏端点相关模型)
    - Hiding properties (隐藏属性)

大多数程序需要控制 用户 能够访问什么数据/服务,通常情况 包括要求用户登录访问受保护的数据, 或者 需要用于 其它应用程序 的 access token来访问受保护的数据  

github 案例: [loopback-example-access-control](https://github.com/strongloop/loopback-example-access-control)  

Loopback 应用通过模型数据访问, 所以控制访问数据意味着将限制模型, 需要指定 谁或什么 可以读/写/执行 什么样的模型方法.  
