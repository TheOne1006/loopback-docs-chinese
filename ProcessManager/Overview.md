# Process Manager(进程管理器)

> **Important**: StrongLoop Process Manager 不能在Windows系统上运行

- Running StrongLoop PM(执行进程管理器)
    - Running StrongLoop PM as a transient process(运行StrongLoop PM作为一个过渡过程)
- Connecting to Process Manager from Arc(从 Arc 连接到 Process Manager)
    - Controlling Process Manager from Arc (从 Arc 控制 Process Manager)
    - Environment variables(环境变量)


StrongLoop Process Manager (StrongLoop PM) 是一个提供进程管理Node.js 应用, 它提供:  

- 构建，打包，和你的 Node 应用程序部署到本地或远​​程系统。
- 查看CPU的配置文件和堆快照,用以优化性能和诊断内存泄漏。
- 保持流程和集群永远活着
- 查看应用程序性能指标。
- 轻松扩展，利用整合的Nginx的负载均衡多台主机

## 执行进程管理器

你可以使用 StrongLoop PM 进行:
- 作为开发和测试过程中暂态过程; 详见下文。
- 作为操作系统服务, 用于生产环境,始终运行它作为一种服务，以确保当系统重新启动或进程停止其他一些原因，它重新启动。

## 执行 StrongLoop PM 作为暂态过程

使用命令 `slc pm` 启动 PM 管理进程作为暂态过程,也就是一个普通（非服务）的应用程序  

启动 pm 管理器, 默认监听 8701, 功能上,它向一个服务,除了该过程将不会自动如果系统重新启动重新启动.  




- - -
