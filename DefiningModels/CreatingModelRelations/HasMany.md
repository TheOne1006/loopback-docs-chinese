## HasMany relations (一对多的关系)

- 简介
- 定义 HasMany 关系
- 添加方法到 HasMany 的模型


### 定义

hasMany 关系建立一个一对多的连接到其他 Model, 经常 其他模型还会设置 `belongsto` 关联到 这个模型。  
这个关系(hasMany) 表示每个实例，都关联着 0 个或者多个其他 model  

例如，在与客户定单的应用程序，客户可以有许多订单，如下图所示。

![has-many](/images/has-many.png)

目标 model (order) 有一个属性, customerId, 作为一个 外键关联到申明的Model(Customer) 的主键 id  


### 定义 HasMany 关系

使用 `slc loopback:relation` 定义两个 model 之间的关系, 工具会提示你输入模型，相关模型的名称，以及其他必要信息, 然后工具将会修改模型定义文件`common/models/modelName.json`  

案例, json 文件:  

> common/models/customer.json

```
{
  "name": "Customer",
  "base": "PersistedModel",
  ...
  "relations": {
    "reviews": {
      "type": "hasMany",
      "model": "Review",
      "foreignKey": "authorId"
    },
...
```

> 另外,也可以在代码中定义,但是不建议  

如果不指定foreignKey(外键), LoopBack 派生关系的名称和外键规则如下:  

- Relation name: 驼峰化 Model Name + 复数格式(默认s) ,如 Order => orders
- Foreign key: 追加的关系名称 "Id", 如 Order 的默认外键是 ordersId


### 添加方法到 HasMany 的模型

一旦定义了 HasMany 关系，Loopback将添加对应的方法到 Model 的原型对象 如: `Customer.prototype.orders(function(){ })`  

| 实例案例方法                | 描述                                 |
| ------------------------- | ------------------------------------ |
| customer.orders([where], function(err, orders) {}); | 过滤查找 属于用户的订单信息 |
| var order = customer.orders.build(data);<br/> 等效于 <br> var order = new Order({customerId: customer.id, ...}); | 建立一个新的订单 用于与客户 customerId设置为客户的ID。没有持久性参与 |
| customer.orders.create(data, function(err, order) { });<br/> 等效于 <br>
Order.create({customerId: customer.id, ...}, function(err, order) { }); | 创建一个属于 该客户的订单 |
| customer.orders.destroyAll(function(err) { }); | 删除所有该用户的订单 |







- - -
