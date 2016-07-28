## Storage component (存储组件)

- 简介
- 安装
    - 案例
- Containers 和 文件
- 创建一个存储组件的数据源
    - 使用命令行工具和JSON
    - 使用 JavaScript配置
    - 提供凭据

### 简介

该[存储组件](https://github.com/strongloop/loopback-component-storage) 能够使得应用方便的上传,下载 与 云存储提供商和本地服务器。  

它有 Node.js 和 REST API 存储管理二进制内容，支持的云供应商有:  

1. 亚马逊
2. Rackspace公司
3. OpenStack
3. Azure  

使用 存储组件能够像 其他 loopback datasource 一样操作一个数据库。   
像其他数据源，它支持创建，读取，更新和删除 （CRUD）操作和 其对应的REST API。  

> 此组件没有提供，开箱即用的方法，使用案例参照案例

### 安装

安装命令:  
```bash
npm install loopback-component-storage
```

##### 案例

案例github地址: <https://github.com/strongloop/loopback-example-storage>

> 默认使用 example-2.0目录 为 loopback 2.0 提供案例, example 目录 为 loopback 1.0 使用的案例

### Containers and files 容器与文件

存储组件 通过 容器和文档 组织内容, 一个容器持有相关联的文档，遍历隶属于它的相关文件。  

- Container(容器)
    - 文件归组,类似一个目录或者 folder(??)
    - 容器定义的命名空间对象，并通过它的名字唯一标识，通常在一个用户帐户
    - **注意**: 容器无法拥有子容器
- files(文件单元)
    - 存储数据, 如文档或图像. 文件是始终在一个（也是唯一一个）容器.
    - 在一个容器中, 文件名必须是唯一的。

### 创建一个存储组件的数据源

创建工具: <https://docs.strongloop.com/display/APIC/Command-line+reference>  

可以使用 命令行工具 或者 `/server/datasources.json`在JavaScript文件 创建.  

##### 使用命令行工具和JSON

```bash
slc loopback:datasource
# OR
apic create --type datasource
```

然后编辑/server/datasources.json并手动添加数据源的属性,

```js
// 本地数据
"storage": {
  "name": "storage",
  "connector": "loopback-component-storage",
  "provider": "filesystem",  // 供应商：“文件系统”
  "root": "./server/storage" // 路径
}

// 远程案例
"myfile": {
   "name": "myfile",
   "connector": "loopback-component-storage",
   "provider": "amazon", //其他相关配置
   "key": "your amazon key",
   "keyId": "your amazon key id"
 }
```

##### 使用 JavaScript配置

在 `server/server.js` 中使用 `loopback.createDataSource()`方法创建存储组件数据源.  

```js
// 本地
var ds = loopback.createDataSource({
    connector: require('loopback-component-storage'),
    provider: 'filesystem',
    root: path.join(__dirname, 'storage')
});

var container = ds.createModel('container');

// 远程
var ds = loopback.createDataSource({
  connector: require('loopback-component-storage'),
  provider: 'amazon',
  key: 'your amazon key',
  keyId: 'your amazon key id'
});
var container = ds.createModel('container');
app.model(container);
```

##### 其他创建文件

> `/server/model-config.json` 增加配置

```js
// 增加 container 的 model
"container": {
  "dataSource": "storage",
  "public": true
}
```

> `/server/models/container.json` 相关 model 配置

```js
{
  "name": "container",
  "base": "Model",
  "properties": {},
  "validations": [],
  "relations": {},
  "acls": [],
  "methods": []
}
```

> `/server/models/container.js` 相关逻辑编程

```js
module.exports = function(Container) {

};
```


##### 提供凭据

具体配置项参照 官网地址 <https://docs.strongloop.com/display/public/LB/Storage+component>  
