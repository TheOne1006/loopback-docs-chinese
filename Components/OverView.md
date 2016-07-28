## LoopBack components (LoopBack 组件)

- 简介
    - Component contract
- 预定义 LoopBack 组件
    - API Explorer
    - OAuth2
    - 推送通知
    - Storage(存储)
    - Third-party login (Passport)

### 简介

Loopback 组件 是扩展一个基本的环回应用预定义的包。  
从根本上说，一个组件是捆绑了相关的代码的单元, 使得 loopback 能够简易重用。  
你可以在 `component-config.json` 配置你的组件。  


最简单的组件，满足组件条件的组件需要暴露一个 方法 `function(app, options)` 作为模块的出口  、

Loopback应用本身不外乎是，一堆组件的组合。  


#### Component contract（组件"契约")

作为Loopback的组件，一个 模块需要暴露出一下类似的方法:  
```js
function(app, options)
```

类似于 Express 的中间件， 不过一个是 `function(options)`, 而 Express 是 `function(req, res, next)`  

### 预定义 LoopBack 组件

Loopback 提供一些预定义组件,如下:  


| Component       | Desc                | Module          |
|-----------------|---------------------|-----------------|
| API Explorer    | Enable Swagger      | loopback-component-explorer |
| OAuth 2.0       | Enable app to oAuth Server | loopback-component-oauth2 |
| Push Notifications | 推送服务 | loopback-component-push |
| Storage component | 增加了一个接口抽象的存储供应商，如S3，文件系统分为一般容器和文件 | loopback-component-storage |
| Synchronization | ???, 同步数据 | 内置于 Loopback |
| Third-party login using Passport | 第三方登录 | loopback-component-passport |
