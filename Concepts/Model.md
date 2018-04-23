# Model

- 概念
- 定义一个Model
- 使用 Legacy Juggler
  - Model 修饰器
  - 属性修饰器
  - 数组属性修饰器
  - JSON模式推断
    - 支持的JSON关键字
- 其他的ORM


### 概念

一个 model 表示 LoopBack 模型的定义, 关联到 数据 juggler.  
目前, 我们提供了 `@loopback/repository` 模块, 它提供了特殊的修饰符来按照顺序 向你的 TypeScript/JavaScript 类添加元数据。


### 定义一个Model

其核心, model 在 Loopback 中是一个简单的 Javascript 类。

类似
```js
export class Customer {
  email: string;
  isMember: boolean;
  cart: ShoppingCart;
}
```

可扩展性是 LoopBack 的核心特点。 使用外部包可以添加一些附加功能。
例如，集成 允许构造器 或者 使用 JSON 模式构建。  
通过 `@loopback/repository` 中的 `@model` 和 `@property` 装饰器, 这些功能可用于 LoopBack 模型。  

```js
import {model, property} from '@loopback/repository';

@model()
export class Customer {
  @property() email: string;
  @property() isMember: boolean;
  @property() cart: ShoppingCart;
}
```

### 使用 Legacy Juggler

通过 legacy juggler 定义一个 model, 你的类将集成 `Entity` 以及使用 `@model` 和 `@property` 修饰符

```js
import {Entity, model, property} from '@loopback/repository';

@model()
export class Product extends Entity {
  @property({
    id: true,
    description: 'The unique identifier for a product',
  })
  id: number;

  @property() name: string;

  @property() slug: string;

  constructor(data?: Partial<Product>) {
    super(data);
  }
}

```

##### 使用 model 修饰符
model 修饰符还可以使用 loopback 的 json 定义方式。

```js
@model({
  name: 'Category',
  properties: {
    // define properties here.
  },
  settings: {
    // etc...
  },
})
class Category extends Entity {
  // etc...
}
```

##### 属性修饰符

属性装饰器采用 LoopBack 3 中用于各个属性条目的相同参数:

```js
@model()
class Product extends Entity {
  @property({
    name: 'name',
    description: "The product's common name.",
    type: 'string',
  })
  public name: string;
}
```

属性装饰器利用LoopBack的元数据包来确定特定属性的类型。

```js
@model()
class Product extends Entity {
  @property() public name: string; // The type information for this property is String.
}
```

##### 数组属性装饰器

由于JavaScript 数组的性质, LoopBack 可以自动腿短元数据的限制。 在 JavaScript 中， 数组不包含㡰成员类型的消息，通过遍历数组， 可以检查数组成员的基本类型(字符串，数组，数字，bool)

为了保持一致性, 我们需要使用 装饰器, 它将为数组属性的类型推断添加适量的元数据。 `@property.array`

```js
@model()
class Order extends Entity {
  @property.array(Product) items: Product[];
}

@model()
class Thread extends Entity {
  // Note that we still require it, even for primitive types!
  @property.array(String) posts: string[];
}

// 还可以有第二种选择 
@model()
class Customer extends Entity {
  @property.array(String, {
    name: 'names',
    required: true,
  })
  aliases: string[];
}
```

### JSON模式构建

Loopback3 的方式
