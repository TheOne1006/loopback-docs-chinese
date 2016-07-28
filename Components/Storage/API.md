## Storage API

一旦你创建一个容器，它会同时提供REST和Node API，如下表所述:  

| 描述             | 容器模块方法 | REST API |
|------------------|-----------|----------|
| 列出所有容器      | getContainers(cb) | GET /api/containers |
| 获取有关指定容器的信息。| getContainer(containerName, cb) | GET /api/containers/:container |
| 创建一个新的容器 | createContainer(options, cb) | POST /api/containers |
| 删除指定的容器   | destroyContainer(containerName, cb) | DELETE /api/containers/:container |
| 列出指定的容器内的所有文件 | getFiles(container, download, cb) | GET /api/containers/:container/files |
| 获取指定容器内指定的文件信息 | getFile(container, file, cb) | GET  /api/containers/:container/files/:file |
| 按名称给定的容器内删除文件 | removeFile(container, file, cb) | DELETE /api/containers/:container/files/:file |
| 上传一个或多个文件到指定的容器中。<br>请求主体必须使用 `multipart / form-data` 的这对HTML文件的输入类型使用 | upload(req, res, cb) | POST /api/containers/:container/upload |
| 下载指定的容器内的一个文件 | download(container, file, res, cb) | GET /api/containers/:container/download/:file |
| 获取上载流 | uploadStream(container, file, options, cb) | |
| 获取下载流 | downloadStream（集装箱，文件选项，CB）| |
