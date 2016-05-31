## BelongsTo relations 隶属关系

- 简介
- 定义 belongsTo 关系
- 方法加入到模型


### 简介  

一个 Model 设置 belongsTo relation(隶属关系) 时同时,另一个 model 相应的设置了 一对一 或者 一对多的关系  

在一个多到一的关系每个声明 "belongs to"的实例,都必定有它隶属的 其他 Model 实例对象(目标对象).  

而对象目标可以有多种关系模式

案例, 客户和订单的关系:

1. 每个订单都属于唯一客户放, "Order belongsTo (one) Customer"(订单隶属于客户)
2. 一个客户可以有多个订单,  "Customer hasMany Orders"

> 一种belongsTo 关系是 另一个 Model 使用 hasMany 关系:  

![belongs-to](/images/belongs-to.png)

Order 这个 model 有一个外键 关联到 目标(Customer) Model 的主键.  
如果主键不存在，Loopback 会自动添加一个.  

> 另一种 belongsTo 是另一个 Model 使用  one-to-one 关系, 类似于 many-to-one, 每个 实例都只属于一个目标实例, 同时目标实例只有一个实例

案例, 客户与地址的关系:  

1. 每个地址可以关联到一个用户, "Address belongsTo (one) Customer".
2. 每个客户只能拥有一个地址, "Customer hasOne Address"

![Capture](/images/Capture.png)


#### 定义 belongsTo 关系

使用 `slc loopback:relation` 定义两个 model 之间的关系, 工具会提示你输入模型，相关模型的名称，以及其他必要信息, 然后工具将会修改模型定义文件`common/models/modelName.json`  

案例, json 文件:  

```json
{
  "name": "Order",
  "base": "PersistedModel",
  "relations": {
    "customer": {
      "type": "belongsTo",
      "model": "Customer",
      "foreignKey": ""
    }
  }
}
```

另外,也可以在代码中定义,但是不建议  

```js
Order.belongsTo(Customer, {foreignKey: 'customerId'});
```

如果声明模型没有一个外键属性, LoopBack 将会添加一个 Model 同名 , 默认关联外键为 Model 的id

如果不指定foreignKey(外键), LoopBack 派生关系的名称和外键规则如下:  

- Relation name: 驼峰化 Model Name ,如 Customer => customer
- Foreign key: 追加的关系名称 "Id", 如 Customer 的默认外键是 customerId




### 方法加入到模型

一点定义了 `BelongsTo` 关系, Loopback 自动添加, 一个关系名相同的方法到 声明的 Model 的 prototype 中,例如  Order.prototype.customer()

根据参数的不同,该方法可以用于设置或者获取所属模型实例。方法调用的结果被内部缓存，并通过后同步调用的方法提供。  

| 实例方法                                      | 描述                    |
| -------------------------------------------- | ---------------------- |
| order.customer(function(err, customer) { }); | 通过订单获得相应用户, 异步 |
| var customer = order.customer()              | 同步获得相应用户          |
| order.customer(customer);                    | 设置订单用户             |

















- - -
