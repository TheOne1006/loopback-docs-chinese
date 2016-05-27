## Creating model relations(创建模型之间的关系)

- model 关系概念
- 使用`slc loopback:relation`
- 关联选项
    - 范围
    - 属性
    - invertProperties ?? 逆属性(?)
    - 自定义范围方法
- 通过REST API 暴露关联对象的方法


### model 关系概念

在工作中单个 model 比较好理解, 但是现实使用中 model 之间往往存在着各种关联。  
当你在实际工作中往往需要很多不同的model, 你可以定义这些属性之间的关系,例如  

1. 1个顾客有多个订单, 同时 每个订单都只属于1个顾客
2. 1个用户可以拥有 一个或多个角色, 每种角色可以有 零个或者多个用户
3. 一个医生可以看见多个病人的预约, 一病人也可以看见多个医生的情况。

通过连接模型, Loopback 暴露一组API来与每个模型实例和查询的互动，并筛选基于客户需求的信息。   

可以定义模型之间的如下关系:  

1. BelongsTo relations
2. HasOne relations
3. HasMany relations
4. HasManyThrough relations
5. HasAndBelongsToMany relations
6. Polymorphic relations
7. Embedded relations (embedsOne and embedsMany)

你可以在 model 定义JSON 文件中 或者 js 代码中定义。  

当你为一个 Model 定义了一个 关系, Loopback 将对这个 Model 添加一组方法, 每种类型的关系, 在文章中详细介绍

> **Important**: 所有的model 都是继承一个 Model Class, 通过他们关系关联, 无论他们的数据库是否相同  


### 使用`slc loopback:relation`

创建现有 model 之间的新关系的最简单方法是使用`$slc loopback:relation`, 关系的名称必须与它所引用的属性不同.


### Relation options(关系选项)

对于大部分关系类型,他们有三个选项:  
- Scope
- Properties
- Custom scope methods

#### Scope 范围

scope 选项可以是一个对象或者 一个方法, 并适用于在相关范围内的所有过滤/条件。  

scope 对象 , 拥有所有过滤选项: `where`, `order`, `include`, `limit`, `offset`, ...  

这些选项合并到默认的过滤器,  

当范围是一个函数，它将接收当前实例，以及默认的过滤器对象。

使用方法:  `/api/Customers/{id or scope}`, id 改成 scope 名  

Demo:  

```js
// only allow products of type: 'shoe', always include products
// 只允许 type 为 'shoe'的 Category 数据 添加 products
Category.hasMany(Product,
                 { as: 'shoes',
                   scope: { where: { type: 'shoe' },
                   include: 'products'
});

// inst is a category - match category type with product type.
// ????
Product.hasMany(Image,
                { scope: function(inst, filter) {
                           return { type: inst.type };
                       }
});  
```

#### Properties(属性)

你可以通过两种方法指定 properties 选项.



#### invertProperties

通常, 属性是从 parent 传给 child 的。



- - -
