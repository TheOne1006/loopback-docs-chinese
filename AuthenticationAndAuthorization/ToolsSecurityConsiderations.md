### Security considerations (安全注意事项)

- Model REST APIs
    - 隐藏REST模型的属性
- CORS
- 防止 XSS 漏洞


#### Model REST APIs

默认情况, Loopback 的 Model 会创建 一套标准的HTTP端点 (增,删,改,查)操作,
在 modelName.json 的 `public` 属性中指定是否公开.  
如果需要隐藏 模型的 REST API, 只需要 将 `public` 的值改成 `false`  


##### Hiding properties

要隐藏暴露在REST模型的属性，需要定义了一个 `hide` 属性. 详细查看 [Model definition JSON file (Hidden properties)](https://docs.strongloop.com/display/LB/Model+definition+JSON+file#ModeldefinitionJSONfile-Hiddenproperties)


#### CORS (Cross-origin resource sharing)

默认情况下,LoopBack 允许 CORS ,使用了 cors 包,  
改变CORS, 可以在 `middleware.json` 里.  

> 如果在使用浏览器客户端时, 需要开启 CORS 配置

```js
// angular js
var myApp = angular.module('myApp', [
    'myAppApiService']);

myApp.config(['$httpProvider', function($httpProvider) {
        $httpProvider.defaults.useXDomain = true;
        delete $httpProvider.defaults.headers.common['X-Requested-With'];
    }
]);
```


#### Mitigating XSS exploits (防止 XSS 漏洞)

Loopback 存储用户的 access token 在 JavaScript 的对象中. 这可能使容易受到跨站点脚本（XSS）漏洞的安全性.  
作为最佳实践，以减轻这些威胁,可以使用合适的 Express 中间件:

- [Lusca](https://www.npmjs.org/package/lusca)
- [Helmet](https://www.npmjs.org/package/helmet)
- Express 3.x [csrf() function](http://expressjs.com/3x/api.html#csrf)
- [Cookies vs Tokens. Getting auth right with Angular.JS](https://auth0.com/blog/2014/01/07/angularjs-authentication-with-cookies-vs-token/)
