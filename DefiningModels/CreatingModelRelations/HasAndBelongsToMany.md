## HasAndBelongsToMany

- 简介
- 定义 hasAndBelongsToMany 关系
    - 通过REST API 添加关系
- 方法加入到模型

### 简介

hasAndBelongsToMany 关系创建 直接  many-to-many(多对多) 的联系关联到 另一个 model,没有介入的第三方 model.  
例如,在应用中有assemblies and parts(组件和部件), 其中每个组件有许多部分，每个部分出现在许多组件, 需要使用这种关系.  

### 定义 hasAndBelongsToMany 关系

使用 ` slc loopback:relation` ....

案例:

![has-and-belongs-to-many](images/has-and-belongs-to-many.png)

`/common/models/assembly.json`:  
```json
{
  "name": "Assembly",
  "plural": "Assemblies",
  "relations": {
    "parts": {
      "type": "hasAndBelongsToMany",
      "model": "Part"
    }
  }
}
```

> 还可以定义在代码hasAndBelongsToMany关系，虽然这是不一般的建议

### 通过REST API添加relation

当通过REST API添加relation, 连接的 Model 必须之前存在.  
例如以上案例, "Assembly" 和 "Part" Model, .... (??)




















- - -
