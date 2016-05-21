## Creating an application (创建一个应用)

> **!important**:
    1. 确保安装 StrongLoop.
    2. 已经阅读 核心概念

- 创建一个新的loopback 应用
- Standard project layout(标准的项目布局)
- Main application script - server.js (启动脚本)

### Creating a new application(创建新应用)

最简单的方式, 使用命令行工具 `$slc loopback`, 它能帮助我们完成依赖包安装,以及提供标准的项目布局.  

通过这种方式创建之后,你想改变一些配置如: 关闭堆栈跟踪, 禁用 API Explorer, 检索环境变量的值,
可以查看 [Environment specific configuration](https://docs.strongloop.com/display/LB/Environment-specific+configuration) 了解更多细节  
