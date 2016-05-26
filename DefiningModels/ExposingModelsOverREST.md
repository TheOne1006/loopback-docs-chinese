## Exposing models over REST(将模型暴露给REST)

- 简介
    - REST 路径
    - 使用 REST 路由
    - 请求数据格式化
        - 使用HTTP查询字符串传递JSON对象或数组
    - 响应数据格式化
    - 禁用 API Explorer
- 预定义的远程方法(remote methods)
- 暴露/隐藏 models, 方法, 端点(endpoints)
    - 隐藏的方法和REST端点
    - 案例: 只读端点(endpoints)
    - 相关模型隐藏端点
    - 隐藏属性


###  简介

Loopback 自动拥有一个组标准的HTTP端点，对创建，读取，更新提供REST的API，并删除模型数据（CRUD）操作。  
在 `model-config.json` 文件中的 `public`(Boolean, true 暴露, false 隐藏 ) 属性指定是否公开 提供这些方法是否暴露到 REST API中。  

### REST路径  

默认情况下，REST API路径 = 模型名称的复数, 如需修改:

- Model.settings.http.path(RESTapi 路径)
- plural: 加复数的字符串
- 默认情况为, 模型名+ 's'


### 使用 REST 路由

默认情况,脚手架 将应用的 model 使用 loopback.router 暴露于 REST 中.  

要手动 暴露 model 于 REST, 使用 loopback.rest 添加使用下面的代码，例如：
```js
// server/server.js
// 暴露内置的 acl
var ACL = app.models.ACL;
app.use(loopback.rest());
app.model(ACL);
```

### 请求数据格式化

对于POST和PUT请求，请求体可以是JSON，XML或urlencoded格式,   
对应的 header Content-Type 为 `application/json`, `application/xml`, `application/x-www-form-urlencoded`,  
在 header __Accept__ 字段 指示其响应格式偏好.


#### 通过 HTTP 请求参数 传递JSON对象或数组

一些 REST Apis 会在 query string (请求参数) 中带着一个对象/或者数组,  
Loopback 支持两种方案解析在 query string 中的对象/数组。

1. node-querystring (qs) 语法
2. 字符串化 JSON

demo
```js
// node-querystring
http://localhost:3000/api/users?filter[where][username]=john&filter[where][email]=callback@strongloop.com
// 字符串化 JSON
http://localhost:3000/api/users?filter={"where":{"username":"john","email":"callback@strongloop.com"}}
```

### 响应数据格式化

所有响应信息均会格式化,一般为 JSON /xml 设置响应的响应头和响应体。  

响应报文案例:  

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://localhost:3000
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
Content-Length: 59
Vary: Accept-Encoding
Date: Fri, 24 Oct 2014 18:02:34 GMT
Connection: keep-alive

{"title":"MyNote","content":"This is my first note","id":1}
```

在JSON格式化的失败的响应会包含:  

```
{
"error": {
    "message": "错误信息",
    "stack": "堆栈跟踪信息",
    "statusCode": 状态码
    }
}
```

#### 禁用 API Explorer

Explorer 在开发模式下是一个好用的程序,但是在生产环境下,为了安全考虑,并不希望暴露出去。  
禁止Explorer步骤:  

1. 设置环境变量 NODE_ENV=production
2. 配置`server/component-config.production.json` 文件中写入

```json
{
  "loopback-component-explorer": null
}
```


### Predefined remote methods(预定义的远程方法)

默认情况下, 它们由一个连接数据源的 Model 提供, Loopback 暴露一组标准的 REST API 提供 增删改查 操作。  

下面的案例中, 定义了一个简易的 Model Location(商业场所信息) 用于说明 通过 Loopback 暴露出来的 REST API。  

Loopback 会自动创建 一批 Node 方法 以及对应的 REST endpoints。  


| Node(Model) API    | HTTP Method        | Example Path |
| ------------------ | ------------------ | ------------ |
| create( )          | POST               | /locations  |
| upsert( )          | PUT                | /locations  |
| exists( )          | GET                | /locations/:id/exists  |
| findById( )        | GET                | /locations/:id  |
| find( )            | GET                | /locations  |
| findOne( )         | GET                | /locations/findOne  |
| destroyById( ) / deleteById( )| DELETE  | /locations/:id  |
| count( )           | GET                | /locations/count  |
| prototype.updateAttributes( )| PUT      | /locations/:id  |
| createChangeStream( )| POST             | /locations/change-stream  |
| updateAll( )       | POST               | /locations/update  |

> 上表提供的方法和REST端点的部分列表。请参阅API文档的所有节点API方法的完整列表。见  PersistedModel REST API  有关REST API的细节。


### 暴露/隐藏 models, 方法, 端点(endpoints)

将 Model 暴露到 REST中的配置, `/server/model-config.json`, 中的 `public` 属性。  

#### 隐藏的方法和REST端点

如果你不希望暴漏某些 create, retrieve, update, delete 操作, 你可以通过 在 model 中 调用`disableRemoteMethod()` 隐藏他们










- - -
