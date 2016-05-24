## 第一部分 notes-app-plain

notes-app-plain 是一个简单的Web应用程序 呈现的笔记列表:  

```md
Notes
--------------
- Buy eggs
- Buy milk
- Buy sausages
```

```md
(Web Server)     (API Server)
 +--------+      +----------+
 | Client |----->| Resource |
 |        |<-----| Server   |
 +--------+      +----------+
```

在OAuth 2.0用户术语中，Web服务器是一个 "客户端应用" , API服务器是一个 "资源服务器",
我们将构建以准备在客户端和服务器资源的应用程序部分在教程2.
