### Accessing related models(访问相关模型)

> When accessing a related model, the active ACL is still the one for the model you are calling. So even if your model has DENY ALL permissions set, if the model relating to it has no ACL, then all the relation endpoints will be open. This can be a security risk because, for example, GET /OpenModel/{id}/ACLSecuredModel will allow full access to ACLSecuredModel through the OpenModel relations.



- Restricting access to related models(限制访问相关模型)
- Querying related models(查询相关模型)

当两个 model 存在关系时,LoopBack自动创建一个相关模型的方法对应于API的路由相关模型的方法.  

> 下面的方法名使用的是双下划线 `__`  



















- - -
