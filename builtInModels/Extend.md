## Extending built-in models(扩展内置 model)

- 使用JSON 扩展
- 使用 js 扩展
    - Model 定义 使用 mixin
    - 建立自定义模型

### 使用JSON扩展模型

使用 模型生成器 创建一个 Model, 你需要选择一个 model 然后继承它,你的模型会继承 它,以及获得他的方法和属性.  
案例,扩展了 PersistedModel:  

`/common/models/model.json`:  

```json
{
  "name": "Order",
  "base": "PersistedModel",
...
}
```

在一般情况下,使用  PersistedModel  当希望使用一个连接器，例如MySQL或MongoDB的存储在数据库中的数据为基础的模型。  

使用 Model 作为base, 为不具有CRUD语义模型，例如，使用连接器，如SOAP和REST  

> 扩展内置的 User Model 来创建自己的 users 或者 customers, 该模型提供 注册,登录,忘记密码等功能.
