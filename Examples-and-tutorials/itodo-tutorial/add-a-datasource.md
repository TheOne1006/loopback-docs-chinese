# 添加一个数据源

- 数据源
- 创建一个数据源
  - config/datasources.json
  - src/datasources/db.satsource.ts


### 数据源

数据源是 loopback 链接各种数据存储的方式，如 数据库, API, 消息队列 等.  
在 LoopBack 4, 数据源可以表示为强类型对象并 注入到 整个应用中。  
通常在 LoopBack4 中, 数据源与存储库结合使用来提供对数据的访问。

由于我们的Todo Api 需要保留 Todo 项目实例， 我们需要创建一个数据源 定义使之成为可能。

### 创建一个数据源

在根目录创建一个 `config` 目录, 并在里面创建 `datasources.json` 文件。  

为了本相乘的目录, 我们使用 将 内存做为存储功能。 并通过 Juggler 提供链接


##### config/datasources.json

```json
{
  "name": "db",
  "connector": "memory"
}
```

在 `src/datasources` 目录中创建一个新文件 `db.datasource.ts`,   
这个文件将 暴露 _强类型_ 的数据 使用 `DataSourceConstructor`,   
我们可以将它注入到程序中。

##### src/datasources/db.datasource.ts


```js
import * as path from 'path';
import {DataSourceConstructor, juggler} from '@loopback/repository';

const dsConfigPath = path.resolve('config', 'datasources.json');
const config = require(dsConfigPath);
export const db = new DataSourceConstructor(config);
```


完成这这一步，我们可以继续下一个课程了
