## 模型关系教程: model relations

```bash
git clone https://github.com/strongloop/loopback-example-relations.git
cd loopback-example-relations
npm install
node .
```

在这个例子中，我们创建一个简单的Web应用程序来演示模型关系的概念,该应用程序包括有一个链接列表一个单一的网页来帮助我们 通过REST查询和过滤样本数据.  


### Prerequisites(先决条件)

- 教程
    - loopback 入门教程
    - [教程step1](https://github.com/strongloop/loopback-example#step1)
- 知识点
    - Loopback引导脚本
    - LoopBack models
    - Loopback model relations
    - Creating loopback model relations

### Try the API  

通过 `node .` 启动应用,通过浏览器访问 3000 端口.  

#### hasMany 类型

- /api/customers 所有用户列表
- /api/customers/4 查看指定ID的客户
- /api/customers/youngFolks 列出预定范围命名 年轻人
- /api/customers/4/orders 指定 ID 的所有订单列表
- /api/customers?filter[include]=orders 所有的用户列表 包含所有的order
- /api/customers?filter[include][orders]=customer 列出所有客户，包括他们的订单还包括客户
- /api/customers?filter[include][orders]=customer&filter[where][age]=21 列出所有客户，包括他们的订单还包括客户通过过滤 用户的 年龄为 21
- /api/customers?filter[include][orders]=customer&filter[limit]=2 列出列表, 同时列表显示2条
- /api/customers?filter[include]=accounts&filter[include]=orders 显示所有用户列表, 关联它们的 账号信息 和 订单信息

#### embedsOne

- /api/customers/6/address 指定用户的地址

#### embedsMany

- /api/customers/7/emails 指定用户的所有 email 列表

#### referencesMany

- /api/customers/5/accounts 列出了一个给定的客户拥有的所有账户
- /api/customers/1/accounts/1 通过外键查找特定客户的特定帐户
- /api/customers/1/accounts/count 通过某个用户 id 查找响应的 账号的统计

#### polymorphic embedsMany (多态 embedsMany)

- /api/Books/1/people 与 Book id 1 的相关用户列表 (作者, 读者)
-



























- - -
