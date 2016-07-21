## Defining mixins(定义 mixins)

- mixin 简介
    - 内置 model 的 mixins
- 创建一个 mixin 脚本
- 在 model-config.js 引入 mixins
- 启动 model 的 mixins


### Overview 简介

使用 mixin 可以将一组公共逻辑添加到 Model 中,
例如 一个 [timestamp mixin](https://github.com/clarkbw/loopback-ds-timestamp-mixin)
将为 model 注入 `created(创建时间)` 和 `modified(修改时间)` 两个属性,并维护响应逻辑.  

也可以使用 mixin 扩展[内置 model]( https://docs.strongloop.com/display/APIC/Extending+built-in+models)

##### 内置 model 的 mixins 方法

> 基本 model

默认情况下,基本的 Loopback 具有 mixin 属性和方法通过以下方式:  

- Inclusion object (包含对象)
- Validateable object

> Connected model

处理包括 `基本 model` 的方法,当 model 连接到 datasource 时:  

- RelationMixin class
- PersistedModel class


### 创建一个 mixin 脚本

mixin 是一个 js 文件,位于以下几个目录中, 防止的目录取决于 mixin 的范围:  

- `common/mixins/mixinName.js` 例如:  `common/mixins/timeStamp.js`
- `server/mixins/mixinName.js`

如果需要在客户端和服务端中使用,那么放置在 common ,如果仅适用于服务器那么放置在 server 中.  

> 以上目录只是建议, 也可以在 `model-config.js` 中设置自定义目录

可以使用 mixin 对不同的 model 进行相同的 方法和 增加模型定义

例如:  

`common/mixins/timestamp.js`
```js

module.exports = function(Model, options) {
  // Model is the model class
  // options is an object containing the config properties from model definition
  Model.defineProperty('created', {type: Date, default: '$now'});
  Model.defineProperty('modified', {type: Date, default: '$now'});
}
```

上述 mixin 代码添加了 两个属性 `created`,`modified`, 并设置了 类型, 相同与设置 `model.json`文件


以下代码, 使得所使用mixin 的 Model, 扩展了一个 `before save` 钩子

```js
module.exports = function(Model, options) {
  'use strict';
  Model.observe('before save', function event(ctx, next) { //Observe any insert/update event on Model
    if (ctx.instance) {
      ctx.instance.squirrel = true;
    } else {
      ctx.data.squirrel = true;
    }
    next();
  });
};
```

### Reference mixins in model-config.js(在 model-config.js 引入 mixins)

`server/model-config.js `文件  
```js
{
  "_meta": {
    "sources": [
      "loopback/common/models",
      "loopback/server/models",
      "../common/models",
      "./models"
    ],
    "mixins": [
      "loopback/common/mixins",
      "loopback/server/mixins",
      "../common/mixins",
      "path/to/other/mixins", // 添加自定义的 mixin
      "./mixins"
    ]
  },
  ...
}
```

### Enable a model with mixins(激活 model 的 mixin 属性)

在一个 model 中启用 mixin ,需要在 model 定义的 json 文件中,添加一个 `mixin` 属性,  
mixin 是一个 对象, 对象的 key 值为 mixin 的名字(文件名, 驼峰转换), 对应的值将作为 `option` 传入 mixin 作为混入时的参数.

```json

{
  "name": "note",
  "base": "PersistedModel",
  ...
  "mixins": {
     "Timestamp": {
       "myOption": 1,
       "anotherOpt": 2
     }
   },
  "properties": {
    ...
  },
  ...
}
```

如上:  common/mixins/timestamp.js
```js
module.exports = (Model, options) => {
    // 在 note model 的混合中
    // options === {"myOption": 1,"anotherOpt": 2};

    // coding
}
```

### 相关 mixin 列表

- <https://github.com/clarkbw/loopback-ds-timestamp-mixin>
    - 维护相关 `创建时间` 和 `更新时间`




- - -
