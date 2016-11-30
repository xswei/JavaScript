### Module

由于历史的原因，JavaScript没有模块(module)机制，而其他的语言比如Python(import),Ruby(require)等都支持模块。这样在实现比较大型复杂的项目时就遇到阻碍。不过在ES6之前，社区已经形成了一些模块加载方案，比如CommonJS和AMD规范，并有相对成熟的库。

ES6从语言层面实现了模块功能，并且实现比较简单，可以取代CommonJS和AMD规范，成为服务器和浏览器通用的模块解决方案。

ES6的模块设计思想是静态化，也就是在编译时就能确定模块的依赖关系以及输入输出的变量。而CommonJS和AMD都是运行时才能确定。

比如在CommonJS中:

```js
let {stat,exists,readFile} = require("fs")

//则会将fs模块整体加载进来，然后只使用3个方法(运行时加载)

```


在ES6中:

```js
import {stat,exits,readFile} from "fs"

//从fs模块加载3个方法，其他方法不加载(编译时加载)

```

在ES6中，模块自动采用严格模式，无论有没有使用'use strict'，模块功能主要有两个命令:

- export

- import

### export命令

export命令用来规定模块的对外接口.

一个模块是一个独立的文件，文件内部所有的变量外部都无法获取。除非用export将其暴露出去。可以export一个变量，也可以export一个函数或类.

```js
function v1(){...}
function v2(){...}

export {
	v1 as fun1,
	v2 as fun2
}

```

**as**关键字可以在export之前对变量名进行修改以对接口进行重命名

### import命令

import命令用来加载export暴露出的模块接口。

```js
import {v1,v2,v3} from "./m"

```

要将引入的变量使用大括号括起来，在引入其他模块变量后也可以使用**as**进行重命名:

```js
import {v1 as value} from "./m"

```

### 模块的整体加载

如果模块中有多个变量，又要全部加载的话，使用`*`表示：

```js
import * from "./m"

```

### module命令

module命令可以取代import语句，达到整体输入模块的目的:

```js
module m from "./m"

```

m模块中的所有变量都可以通过m.xxx形式访问

### export default命令

在上述例子中，使用import时都必须知道目标模块中export的变量名，而使用export default则不需要，因为使用export default命令时，模块内的变量名会被忽略。

export default命令可以将函数使用默认输出，在其他模块中引入时可以为函数其指定任意名字：

```js

//m.js
export default function(){
	console.log("foo");
}

---------

import fun1(任意指定) from "./m"

fun1();	//"foo"

```

### ES6模块加载的实质

在CommonJS中，模块的输入是被输出值的拷贝，也就是一旦输出后，模块内部的变化不会影响到这个值。而ES6的模块运行机制不同，ES6的模块import之后不回去执行模块，而是会生成一个动态的只读引用。当需要执行时候再去执行，如果模块中的值变了，import之后的值也会变化。