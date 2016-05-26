### 验证模型数据

需要在 Model 添加 Schema (模式 与 mongoose Schema 相同概念),确保数据库能够正确存储.  

一个 Model 数据可以在它进入数据存储之前进行验证, 确保后端架构程序.  

- 添加 schema 到 Model
- 使用验证方法
- 本地化验证消息(中文)


#### 添加模式(schema) 到模型

验证数据的一种方法是创建一个模型模式(Model Schema), Loopback 会确保它的数据符合定义的模式( Schema).  

案例, 一个 product model, 定义了两个属性: 名称，必填且为字符串类型 和 价格，可选 但必须为 数字属性.  

`common/models/product.js`  

```js
var productSchema = {
  "name": { "type": "string", "required": true },
  "price": "number"
};
var Product = Model.extend('product', productSchema);
```

如果一个客户端试图保存额外的属性（例如，产品的描述）, 在该应用保存在模型中的数据之前这些属性被除去.  

#### 使用验证方法

连接到持久性数据源的每个模型都有验证方法从 Validatable mixed  

- Absence 缺少
- Exclusion 排除


| Method(方法)            | Description(描述)   |
| ---------------------- | ------------------- |
| validatesAbsenceOf( )  | 验证缺少一个或者多个属性, 一个 Model 不应该有效的属性,当需要验证的字段不为空验证失败 |
| validatesExclusionOf() | 验证排除。需要一个属性值未在指定数组中。(不包含) |
| validatesFormatOf()    | 验证格式。需要一个模型为包括给定的格式相匹配的属性。 |
| validatesInclusionOf() | 验证纳入集中。需要属性为指定数组中的值.(包含) |
| validatesLengthOf()    | 验证长度。需要一个属性长度成为指定范围内, 三种验证的 "min","max","is", 默认错误提示信息 <br> <ul><li>min: too short</li> <li>max: too long</li> <li>is: length is wrong</li></ul> |
| validatesNumericalityOf() | 验证numericality。需要属性的值是一个整数或数字。|
| validatesPresenceOf()  | 验证一个存在或多个指定的属性。需要一个模式，包括被认为有效的属性; 当验证字段为空失败。 |
| validatesUniquenessOf() | 验证唯一性。确保的属性的值是模型是唯一的。不适用于所有连接器。目前支持这些接口：<br> <ul><li>Memory</li> <li>Oracle</li> <li>MongoDB</li></ul> |


使用这些方法来执行特定的数据验证; 例如:  

```js
// common/models/user.js
module.exports = function(user) {
  // 必须有的属性 name , email
  user.validatesPresenceOf('name', 'email');
  // password , min 提示 Password is too short
  user.validatesLengthOf('password', {min: 5, message: {min: 'Password is too short'}});
  // 只能选择 male / female
  user.validatesInclusionOf('gender', {in: ['male', 'female']});
  // 不能包含 www, billing, admin
  user.validatesExclusionOf('domain', {in: ['www', 'billing', 'admin']});
  // 数字
  user.validatesNumericalityOf('age', {int: true});
  // email 唯一
  user.validatesUniquenessOf('email', {message: 'email is not unique'});
});
```

> 当你调用的验证方法被调用 isValid() 的方法对模型实例进行认证,以及在 模型实例 在创建或更新是自动认证, 所以也不必对 数据进行 isValid()

> 当调用 `upsert()` 是需要进行强制验证, 确保它的 validateUpsert 选项 设置为 true, 在 `common/models/model.json`, 默认情况 通过 model generator 设置为 true

手动调用 `isValid()` Demo:  

```js
user.isValid(function (valid) {
    if (!valid) {
        user.errors // hash of errors {attr: [errmessage, errmessage, ...], attr: ...}   
    }
});
```

另一个Demo, 使用正则表达式定义验证限制:  

```js
// regular expresson 正则表达式
var re = /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
// 关键字 with
UserModel.validatesFormatOf('email', {with: re, message: 'Must provide a valid email'});
//
if (!(UserModel.settings.realmRequired || UserModel.settings.realmDelimiter)) {
    UserModel.validatesUniquenessOf('email', {message: 'Email already exists'});
    UserModel.validatesUniquenessOf('username', {message: 'User already exists'});
}
```

只需要将验证规则添加到 Model 就可以,在创建 Model 的时候回自动进行验证.  

使用 `isValid()` 是一个额外的 ad-hoc(??) 方式进行验证,
你也可以使用  `validate()` 或 `validateAsync()` 自定义验证方法



#### 本地化验证消息(中文) ?? 在哪个目录中添加

不必要在服务中修改错误提示信息,你可以设置本地化的错误信息.  
验证错误回复信息包含 error codes 在 `error.details.codes` 中, 这使客户能够映射错误的本地化消息。

下面是一个例子错误响应:  

```json
{
  "name": "ValidationError",
  "status": 422,
  "message": "The Model instance is not valid. See error object `details` property for more info.",
  "statusCode": 422,
  "details": {
    "context": "user",
    "codes": {
      "password": [
        "presence"
      ],
      "email": [
        "uniqueness"
      ]
   },
    "messages": {
      "password": [
       "can't be blank"
     ],
      "email": [
        "Email already exists"
      ]
    }
  }
}
```







- - -
