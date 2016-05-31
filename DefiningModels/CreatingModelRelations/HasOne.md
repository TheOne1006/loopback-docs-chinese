## HasOne

- 简介
- 定义 HasOne 关系
- 添加方法到 HasOne 的模型

### 简介

HasOne 关系设置了, 一个与其他 模型的一对一关联。每一个 model的实例有且仅有一个 关联到目标 Model 实例的对象。  

### 定义 HasOne 关系

使用命令行工具 `$slc loopback:relation`  

案例, 供应商 和 账号信息。  


> common/models/supplier.json 供应商 Model

```json
{
  "name": "supplier",
  "base": "PersistedModel",
  "idInjection": true,
  "properties": {
    "name": {
      "type": "string"
    }
  },
  "validations": [],
  "relations": {
    "supplier_acct": {
      "type": "hasOne",
      "model": "account",
      "foreignKey": "supplierId"
    }
  },
  "acls": [],
  "methods": []
}
```
一个供应商有一个帐户, 在 account Model 中申明外键 `supplierId`  

> common/models/account.json

```json
{
  "name": "account",
  "base": "PersistedModel",
  "idInjection": true,
  "properties": {
    "id": {
      "type": "number",
      "required": true
    },
    "acctmgr": {
      "type": "string"
    },
    "supplierId": {
      "type": "number",
      "required": true
    }
  },
  "validations": [],
  "relations": {},
  "acls": [],
  "methods": []
}
```

> 你可以在脚本中添加这种关系, 但是不建议  

如果不指定foreignKey(外键), LoopBack 派生关系的名称和外键规则如下:  

- Relation name: 驼峰化 Model Name + 复数格式(默认s) ,如 Order => orders
- Foreign key: 追加的关系名称 "Id", 如 Order 的默认外键是 ordersId

### 添加方法到 HasOne 的模型

一旦定义了 hasOne 关系，Loopback将添加对应的方法到 Model 的原型对象 如: `supplier.prototype.account(function(){ })`  

| 实例方法          | 描述                           |
| ---------------- | ----------------------------- |
| supplier.account(function(err, account) {}); | 找到供应商账号 |
| var supplier = supplier.account.build(data);<br/> 等效于 <br> var account = new account({supplierId: supplier.id, ...});  | 建立一个新的帐户，用于与供应商ID供应商设置为供应商的标识,不会涉及数据库(??) |
| supplier.account.create(data, function(err, account) {}) <br/> 等效于 <br> account.create({supplierId: supplier.id, ...}, function(err, account) { }) | 通过 supplier 实例创建一个新的 账号数据,如果存在会报错 |
| supplier.account.destroy(function(err) { })  | 销毁 account 实例, 通过 supplier 实例 |
| supplier.account.update({balance: 100}, function(err, account) { }) | 更新相关账号 |
