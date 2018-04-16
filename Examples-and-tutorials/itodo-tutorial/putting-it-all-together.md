# 整合

原文: <https://loopback.io/doc/en/lb4/todo-tutorial-putting-it-together.html>

- 整合
  - src/application.ts
- 尝试启动
- 更多案例和教程

### 整合

现在我们已经做好了准备工作了，接下来就是将它们关联到我们的应用中，
Loopback 提供了 [Dependency injection](https://loopback.io/doc/en/lb4/Dependency-injection.html) _依赖注入_ 系统可以将它们整合起来。  

Loopback 的 启动模块 [boot module](https://github.com/strongloop/loopback-next/tree/master/packages/boot) _启动模块_
会自动发现我们的控制器，存储库，数据源和其他工件，并将它们注入我们的应用程序以供使用


> 注意: 启动模块会 在约定的目录文件夹中 发现和注入相关代码，例如
  - 控制器: `./src/controllers` 
  - 数据源: `./src/datasources`
  - Model: `./src/models`
  - 仓库: `./src/repositories`


### src/application.ts


```js
pplication.ts
import {ApplicationConfig} from '@loopback/core';
import {RestApplication, RestServer} from '@loopback/rest';
import {MySequence} from './sequence';
import {db} from './datasources/db.datasource';

/* tslint:disable:no-unused-variable */
// Binding and Booter imports are required to infer types for BootMixin!
import {BootMixin, Booter, Binding} from '@loopback/boot';

// juggler and DataSourceConstructor imports are required to infer types for RepositoryMixin!
import {
  Class,
  Repository,
  RepositoryMixin,
  juggler,
  DataSourceConstructor,
} from '@loopback/repository';
/* tslint:enable:no-unused-variable */

export class TodoListApplication extends BootMixin(
  RepositoryMixin(RestApplication),
) {
  constructor(options?: ApplicationConfig) {
    super(options);

    // Set up the custom sequence
    this.sequence(MySequence);

    this.projectRoot = __dirname;
    // Customize @loopback/boot Booter Conventions here
    this.bootOptions = {
      controllers: {
        // Customize ControllerBooter Conventions here
        dirs: ['controllers'],
        extensions: ['.controller.js'],
        nested: true,
      },
    };

    this.setupDatasources();
  }

  setupDatasources() {
    // This will allow you to test your application without needing to
    // use a "real" datasource! 链接到一个真正的数据库
    const datasource =
      this.options && this.options.datasource
        ? new DataSourceConstructor(this.options.datasource)
        : db;
    this.dataSource(datasource);
  }

  async start() {
    await super.start();

    const server = await this.getServer(RestServer);
    const port = await server.get<number>('rest.port');
    console.log(`Server is running at http://127.0.0.1:${port}`);
    console.log(`Try http://127.0.0.1:${port}/ping`);
  }
}
```

### 尝试启动
让我们试一下我们的应用

```bash
$ npm start
```

下一步, 可以通过 [API Explorer](http://localhost:3000/swagger-ui) 浏览你的API 以及尝试调用它

这里你可以尝试以下几个请求:
- `POST /todo` 以及请求体为 `{ "title": "get the milk" }`
- `GET /todo/{id}` 使用你刚才POST返回数据中获得的ID,你可以看见自己常擦提交的TODO 对象
- `PATCH /todo/{id}`以及请求体为 `{ "desc": "need milk for cereal" }`

至此, 你已经创建了你的第一个 Loopback 4 的应用。

### 更多案例和教程

 希望学习更多 loopback4? 可以从 git 中检出先关案例, 阅读更多信息。