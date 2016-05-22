### Customizing models(自定义 model)

当使用 `slc loopback:model` 创建时,就已经开始了自定义 model.

- loopback 命令行工具 `slc loopback:model`
- 自定 model 的 json 文件
    - 使用另一个 model进行继承
    - 其他设置
- 自定 model 的 js 文件
    - 更改内置方法
    - 通过 boot script
    - 在model 脚本中

#### Customizing a model using JSON (自定 model 的 json 文件)

文件 `common/models/*.json`

##### 使用另一个 model进行继承

可以选择一个现有的 model 进行继承,或者从 Loopback 的内置 model 里选择继承类. 在 model.json 里的 `base` 的属性里设置.

> 一般来说, 使用PersistedModel为基准模型时,当想要把数据存储到数据库中,可以使用一个连接器.
    User Model 作为基础类,它们没有了 CURD 语义 (???)

##### Customizing other model settings(其他设置)

以下是一些主要配置:  








- - -
