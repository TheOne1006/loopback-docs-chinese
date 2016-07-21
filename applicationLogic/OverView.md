## Adding application logic (添加应用程序逻辑)

构建一个应用时,通常需要实现自定义逻辑来处理数据,并响应客户端请求之前执行其他操作.
在 Loopback 中,可以通过以下三种方法实现.  

- 添加 model 逻辑 - 使用 `remote methods`,` remote hooks` 和 `operation hooks`.
- 启动脚本 - 目录 `/server/boot` 中的文件,在应用启动之初执行
- 中间件 - 添加自定义中间件
