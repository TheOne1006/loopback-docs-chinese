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

- **plural**(复数): 设置为自定义字符串值,而不是默认的标准复数形式
- **strict**(严格模式): 设置为 true 时,model 保存一个实例时,实例必须含有定义的属性,而多余的属性也不会存到数据源中.默认`false`
- **IdInjection**(id 注入): 是否自动的添加一个id属性到model 中, 默认 TRUE
- **http.path** - 自定义 REST 暴漏路径

#### Customizing a model with JavaScript code(定义model 在js文件中)

以编程方式扩展模型的基本方法是编辑模型的JavaScript文件中的 `common/models/*.js`,
模型定义后，脚本立即执行,把脚本作为模型的一部分,用于模型的配置和方法实现.  

您还可以添加模型关系, 复杂的验证, 或者某些功能的默认属性: json 文件无法完成的事。  

__注意__, model 里无法直接访问应用程序实例。  

你还可以添加自己的 remote 方法 , 或者 model hook 方法。  

如果不希望将方法暴漏到 REST 中, 只需要注释掉相应的 `remoteMothod()`  


#### Change the implementation of built-in methods(更改内置的执行方法)

##### 通过 boot script

当一个 model 通过数据源来做持久性处理，它成为一个持久化模型扩展PersistedModel 以及 Loopback自动赋予了一套CRUD操作内建的方法。  

某些需求,要求改变他们的默认行为,可以 通过 boot script 修改:  

```js
// /server/boot/script.js
module.exports = function(app) {
  var Note = app.models.Note;
  var find = Note.find;
  var cache = {};

  Note.find = function(filter, cb) {
    var key = '';
    if(filter) {
      key = JSON.stringify(filter);
    }
    var cachedResults = cache[key];
    if(cachedResults) {
      console.log('serving from cache');
      process.nextTick(function() {
        cb(null, cachedResults);
      });
    } else {
      console.log('serving from db');
      find.call(Note, function(err, results) {
        if(!err) {
          cache[key] = results;
        }
        cb(err, results);
      });;
    }
  }
}
```

#### Via your model's script (通过 model 定义脚本修改)

在 `common/models/*.js` 对应的文件中修改默认行为  

demo
```js
module.exports = function(MyModel) {
  // 在附属到数据源之后
  MyModel.on('dataSourceAttached', function(obj){
    var find = MyModel.find;
    MyModel.find = function(filter, cb) {
      return find.apply(this, arguments);
    };
  });
};
```

other demo:

```js
// 2.0 版本后
Customer.deleteById = function(id, cb) {
    // 不执行物理删除
    Customer.update({id: id}, {deleted: true}, cb);
}



```















- - -
