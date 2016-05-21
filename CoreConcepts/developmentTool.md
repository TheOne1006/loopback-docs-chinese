### development tool(开发工具)

LoopBack 提供了两组基本的应用开发工具:  
- slc loopback, 是个命令行工具用于创建和修改LoopBack应用。
- StrongLoop Arc, 是个图形化工具拥有开发，部署和监控LoopBack应用。

#### slc 命令行
slc 命令行工具将辅助开发人员在整个开发周期中生成和修改各种应用组件:

- 使用应用生成器 迅速生成原型应用(scaffold) - `slc loopback`.
- 使用 model generator 生成数据模型及属性 - `slc loopback:model`.  
- 如果需要在已有的模型中添加额外的属性，你也可以使用 property generator - `slc loopback:property`.
- 使用 data source generator 添加数据源 - `slc loopback:datasource`.
- 使用 relation generator 添加模型间的关系 -  `slc loopback:relation`.
