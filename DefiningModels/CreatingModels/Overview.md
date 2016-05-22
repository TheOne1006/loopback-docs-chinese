### 模型创建

- 简介
- 在 JavaScript 中获取 model 的引用
    - 在 JavaScript 文件中
    - 在 boot script 中


#### 简介

你可以通过任何方式创建 Loopback 的 model, 取决使用什么样的数据源.

- 使用`$slc loopback:model` 创建自定义 Model
- 使用 `Datasource.buildModelFromInstance()` 动态创建 无模式(schema-less)的 Model, 详情 [ Creating models from unstructured data  ?? ](https://docs.strongloop.com/display/zh/Creating+models+from+unstructured+data)
- 连接到数据库之后, 会映射到一张 table 上 ??

三种方式都需要一个 [Model definition JSON file](https://docs.strongloop.com/display/LB/Model+definition+JSON+file) 文件,在 loopback 定义, 一般都在 `common/models/` 目录中  


#### 在 JavaScript 中获取 model 的引用

##### 在 JavaScript 文件中

在相关的模型文件中, model 通过顶层函数的参数传入, 所以在函数中是能够直接使用的

##### 在 boot script 中

在 boot script 中可以使用 app.models 获得引用:  

demo
```js
module.exports = function(app) {
  var User = app.models.user;
  var Role = app.models.Role;
  var RoleMapping = app.models.RoleMapping;
  var Team = app.models.Team;
}
```
