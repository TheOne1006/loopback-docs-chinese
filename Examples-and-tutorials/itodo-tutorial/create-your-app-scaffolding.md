# 创建应用的脚手架

原文: <https://loopback.io/doc/en/lb4/todo-tutorial-scaffolding.html>

- 创建应用的脚手架
- 目录结构
- 导航


### 创建目录脚手架

Loopback 4 工具 附带了允许生成整个应用的模板， 包含(例如: 控制器, 模型, 资源)。

使用命令行工具，通过交互方式构建一个应用


### 目录结构

目录结构如下:

```
src/
  controllers/
    README.md                    # 提供 controllers 目录相关信息，如何创建新的 controller 以及 更多信息
    ping.controller.ts           # 基础的 controller 用于相应 GET /ping
  datasources/
    README.md                    # 提供 datasources 目录相关信息, 如何创建新的 datasource 以及 更多信息
  models/
    README.md                    # 提供 models 目录相关信息, 如何创建新的 model 以及 更多信息
  repositories/
    README.md                    # 提供 repositories 目录相关信息, 如何创建新的 repositories 以及 更多信息
  application.ts                 # 应用类, 继承与 [RestApplication](http://apidocs.strongloop.com/@loopback%2frest/#RestApplication) 应用的根类， 并且是你配置应用的地方。
  index.ts                       # 微服务的起点, 创建了一个应用的实例， 运行 booter 将会尝试启动 RestServer 实例绑定到应用上。
  sequence.ts                    # Sequence类的扩展 用于定义在REST请求/响应期间要采取的一组操作
test/
  README.md                      # 请将测试代码放置与这个目录下
  mocha.opts                     # mocha 配置
  ping.controller.test.ts        # 对应 ping.controller.ts 的测试代码
node_modules/
  ***
LICENSE                          # MIT license
README.md                        # readme
index.js                         # 用于执行应用程序的顶级连线 ？
index.ts                         # 允许导入 src 文件夹的内容
package.json
tsconfig.json                    # TypeScript 项目配置, 详见<http://www.typescriptlang.org/docs/handbook/tsconfig-json.html>
tslint.build.json                # ts 配置
tslint.json                      # ts 配置
```
