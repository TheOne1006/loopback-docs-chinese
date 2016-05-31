## Creating database tables for built-in models(创建数据库表的内置机型)

Loopback 应用会使用已小部分内置模型, 要创建这些模型数据库表, 按照一般程序用于 创建模型,
从创建模型数据库模式使用 auto-migration  

> **Important**: 如果数据表已存在,使用 `automigrate()` 将会 删除数据然后重新创建,然后加入新的数据.  
避免这种问题可以使用 `autoupdate()`








- - -
