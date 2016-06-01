## Creating database tables for built-in models(创建数据库表的内置机型)

Loopback 应用会使用已小部分内置模型, 要创建这些模型数据库表, 按照一般程序用于 创建模型,
从创建模型数据库模式使用 auto-migration  

> **Important**: 如果数据表已存在,使用 `automigrate()` 将会 删除数据然后重新创建,然后加入新的数据.  
避免这种问题可以使用 `autoupdate()`,

要使用 内置 Model 创建对应的表, 步骤如下:  

1. 按照基本程序  附加模型数据源  从内存中的数据源到要使用的数据库更改。
2. 建立`server/create-lb-tables.js` 具有下列文件
3. 执行脚本


```js
var server = require('./server');
var ds = server.dataSources.db;
var lbTables = ['User', 'AccessToken', 'ACL', 'RoleMapping', 'Role'];
ds.automigrate(lbTables, function(er) {
  if (er) throw er;
  console.log('Loopback tables [' + lbTables + '] created in ', ds.adapter.name);
  ds.disconnect();
});
```





- - -
