# 添加 legacy juggler

- 添加 legacy juggler
  - src/application.ts
- 导航


### 添加 legacy juggler

Legacy Juggler 是一个 "桥梁" 用于沟通 现有的 [loopback-datasource-juggler](https://github.com/strongloop/loopback-datasource-juggler) 与新的 loopback4 应用构建。  
它归功访问 持久层 / api 所需的功能， 以及 CURD 操作 这些数据源。

它也提供一些方法和接口 用来设置我们的新 LoopBack 应用，这就是我们了解这章节的原因。  

现在你可以开始设置了, 现在是时候修改它来添加他们 通过 `@loopback/repository`， 安装依赖 `npm i --save @loopback/repository`  

下一步 修改 `src/application.ts` 修改 基础类， 使用 `RepositoryMixin`
