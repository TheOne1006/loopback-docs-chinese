# 序列

原文: <http://loopback.io/doc/en/lb4/Sequence.html>

- 什么是序列?
- 元素
- Actions
- 自定义序列
- 高级主题
  - 自定义序列操作
  - 查询字符串参数
  - 解析请求
  - 调用控制器方法
  - 编写 response
  - 发送错误
  - 保持你的序列

### 什么是序列

序列是一个无状态的动作分组，用于控制服务器如何响应请求。  

序列的规则很简单: 它必须产生对请求的响应,创建你自己的序列可以让你完全控制你的服务器实例如何处理请求和响应。DefaultSequence看起来像这样:

```js
class DefaultSequence {
  async handle(request: ParsedRequest, response: ServerResponse) {
    try {
      const route = this.findRoute(request);
      const params = await this.parseParams(request, route);
      const result = await this.invoke(route, params);
      await this.send(response, result);
    } catch (err) {
      await this.reject(response, err);
    }
  }
}
```

### 元素

上面案例总， `route`, `params` 以及 `reult` 都是元素。 当绑定序列, 您使用LoopBack元素来响应请求:

- Route
- Request
- Response