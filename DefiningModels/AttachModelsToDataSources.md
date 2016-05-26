## Attaching models to data sources(将model 链接到数据源)

- 简介
- 添加一个数据源
- 添加数据源凭据
- 制作模型使用的数据源


### 简介

数据源让 model 得以在后端系统的访问和修改数据，例如关系型数据库。  
数据源封装了 model 到 各种后端系统(数据库) 之间的交换数据逻辑。(ORM)  
数据来源一般提供创建，检索，更新和删除（CRUD）功能。  

Model 通过 _connectors_(连接器) 访问数据源, 连接器是可扩展和可定义的。  
一般情况代码中不会直接使用 连接器, 而是由 数据源类 配置底层连接器的API。  
开发模式下,使用 内置的内存连接器.  

需要使用其他的数据源:  

1. 使用 `$slc loopback:datasource` 创建新的数据源
2. 编辑 `datasources.json` 配置数据源信息
3. 创建一个模型来连接到数据源或修改现有模型定义中使用的连接器。

### 添加一个新的数据源

方法:
1. 使用命令行工具 `slc loopback:datasource`
2. 使用`$slc arc`, 图形化创建
3. 最后在 `datasources.json` 中生成相关信息


### 添加数据源凭据

在 `datasources.json` 文件中添加连接数据的信息，通常含有: hostname, username, password, port, databaseName.  

demo:  
```json
"corp1": {
    "name": "corp1",
    "connector": "mysql",
    "host": "your-mysql-server.foo.com",
    "user": "db-username",
    "password": "db-password",
    "database": "your-db-name"
  }
```

### 使用datasource

在 `server/model-config.json` 修改相关配置
```json
"myModel": {
    "dataSource": "corp1",
    "public": true
  }
```


- - -
