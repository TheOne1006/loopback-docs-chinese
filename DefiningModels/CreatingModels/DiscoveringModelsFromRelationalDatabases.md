### Discovering models from relational databases(关系型数据库连接)

- 简介
    - 基础步骤
- discovery 案例
- 添加 discovery 方法

#### 简介

Loopback可以很方便地从现有的关系型数据库创建model, 这个过程被称为 __discovery__ ，由以下连接器的支持.  

- MySQL 连接器
- PostgreSQL 连接器
- Oracle 连接器
- SQL Server 连接器

对于像 Mongodb 的 NoSQl 数据库,使用 [instance introspection](https://docs.strongloop.com/display/public/LB/Creating+models+from+unstructured+data) 代替


##### 基础步骤

按照以下基本步骤:  

1. 使用脚本如以下 discovery 的模式
2. 使用`fs.writeFile()` 保存输出到 `common/models/model-name.json`
3. 添加新的model 到 `server/model-config.json`
4. 使用 `node .`启动应用
5. 使用 `LoopBack Explorer` 验证

#### discovery 案例

下面代码使用了 Oracle 数据库,
第一步创建 Oracle 的数据源。
然后使用 `discoverAndBuildModels()`方法从 table 中创建model

```js
var loopback = require('loopback');
var ds = loopback.createDataSource('oracle', {
  "host": "demo.strongloop.com",
  "port": 1521,
  "database": "XE",
  "username": "demo",
  "password": "L00pBack"
});

// 发现以及创建 models 从 INVENTORY 表, 是否有主键，外键
ds.discoverAndBuildModels('INVENTORY', {visited: {}, associations: true},function (err, models) {
    // 这里我们可以使用 models 方法了
    // 测试model 方法是否能使用
    models.Inventory.findOne({}, function (err, inv) {
        if(err) {
            console.error(err);
            return;
        }
        console.log("\nInventory: ", inv);
        // Navigate to the product model
        inv.product(function (err, prod) {
            console.log("\nProduct: ", prod);
            console.log("\n ------------- ");
        });
    });
});
```

#### 添加 discovery 方法



#### 案例

#### 添加检测方法















- - -
