### Standard project layout(标准的项目布局)

在 Loopback 根目录下有三个 子目录:

- server - Node 应用脚本和配置文件
- client - 客户端 JavaScript, HTML, 和 CSS 文件.
- common - 文件共用客户端和服务器. 子目录 common/models 目录 包含所有模型JSON和JavaScript文件
- definitions - API and product definition YAML files (IBM API Connect only).


#### 目录结构

```
|- node-modules/
|- package.json
|- README.md
|- server/       后台服务端目录
|  |- boot/      初始化脚本路径, 里面的文件会按照字母顺序执行.
|  |- config.json 应用程序配置文件  |   可以通过 app.get('setting-name')获取
|  |- component-config.json  [LoopBack组件]加载的配置
|  |- datasources.json 数据源配置 | 可以通过 app.datasources['datasource-name'] 获取
|  |- middleware.json 中间件定义文件
|  |- model-config.json Model 定义文件
|  |- server.js  核心启动文件
|
|- client/
|  |- README.md
|  |- other
|
|- common/   共享文件夹
|  |- models/ 自定义模型文件夹 | myModel = app.models.myModelName
|     |- model-name.js   模型逻辑脚本
|     |- model-name.json 模型配置脚本 | 小写
|
```


[LoopBack组件]: https://docs.strongloop.com/display/LB/LoopBack+components
