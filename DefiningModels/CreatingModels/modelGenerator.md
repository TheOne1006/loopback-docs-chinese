### Using the model generator(model 构建器)

- 简介
- 基础使用
    - 添加默认值

#### Overview(简介)

最简单的创建 model 方式是通过 命令行工具`$slc loopback:model` 方式创建,
当创建的时候,需要根据工具的提示问题来创建.  

当你创建一个Model 时:

- 会创建 `/common/models/myModel.json` ,数据模型配置文件
- 会创建 `/common/models/myModel.js` , 编写额外的模型逻辑
- 在 `/server/model-config.json` 添加一条相关model 的信息,指定模型的数据源.


#### Basic use(基础使用)

在 loopback 应用的根目录, 使用 `slc loopback:model`命令  

将会询问:  

1. Enter the model name?
    - 为model起一个名字
2. Select the data-source to attach undefined to:
    - 选择一个附属的数据源
3. Select model's base class
    - 选择继承的类
4. Expose [model] via the REST API?
    - 是否暴露这个 model 的 REST API
5. Custom plural form (used to build REST URL)
    - 自定义路径, 默认 (modelName + 's')
6. Common model or server only?
    - 公共model 还是 只用于服务端
7. 之后根据提示创建相关 属性信息


#### Adding default values(添加默认值)

设置默认值属性的一种方式是设置在 `modelName.json` 文件的 "default"属性
