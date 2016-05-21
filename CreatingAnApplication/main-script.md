## Main application script - server.js (启动脚本)

以下案例为 构建器生成代码:  

```js

/**
 * 加载 model, 创建基础 app
 */
var loopback = require('loopback');
var boot = require('loopback-boot');
var app = module.exports = loopback();

/**
 * 初始化应用
 */
boot(app, __dirname);
app.start = function() {

  // start the web server
  return app.listen(function() {
    app.emit('started');
    console.log('Web server listening at: %s', app.get('url'));
  });
};

// start the server if `$ node server.js`, ??
if (require.main === module) {
  app.start();
}
```
