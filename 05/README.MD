### Proxy和Reflect Promise

### Proxy和Reflect

#### Proxy

Proxy用来修改某些操作的默认行为，可以理解为在目标对象前设置一个拦截层，外界对该对象的访问都必须通过这层拦截，从而实现对属性值的访问、修改以及对属性的其他操作的监视。

```js
var obj = new Proxy({},{
	get:function(target,key,receiver){
		console.log("get value");
		return Reflect.get(target,key,receiver);
	},
	set:function(target,key,value,receiver){
		console.log("set value");
		return Reflect.set(target,key,value,receiver);
	}
})

obj.count = 1;
// set value
obj.count;
// get value
// 1

```

在对目标对象设置拦截时候首先要为目标对象生成一个Proxy实例，然后对实例进行操作，而不是对目标对象进行操作:

```js
var proxy = new Proxy(target,handler);

// target为目标对象，handler也是一个对象，用来定义拦截行为。

```

对属性的操作有读取、设置值、删除属性等等，如果没有设置对应的拦截操作则默认为直接对原对象操作，比如设置了get拦截，但是没有设置set拦截，那么在读取值时会执行拦截，而在设置值时直接设置，不进行任何操作。

#### Proxy实例的方法

**get()**方法

get方法用以拦截对某个属性的读取操作

```js
var proxy = new Proxy(target,{
	get:function(target,property){
		if(property in target){
			return target[property];
		}else{
			return "No property in target";
		}
	}
})

//如果存在对应的属性则返回属性值，如果不存在就返回"No property in target"，而不是Undefined

```

**set()**

set方法用来拦截对某个属性的赋值操作

```js
var proxy = new Proxy(target,{
	set:function(target,prop,value,receiver){
		if(prop === 'age'){
			if(value<0 || value>120){
				throw new Error("Error age!")
			}
		}
		target[prop] = value;	
	}
})

//如果设置的属性名为age则限制值的范围，否则抛出错误

```

**apply()**

apply方法用以拦截函数的调用，call以及apply，apply方法接受三个参数:目标对象，目标对象上下文(this)以及目标对象参数数组

```js
var target = function(){
	return "I am the target";
}
var handler = {
	apply:function(){
		return "I am the proxy";
	}
}
var p = new Proxy(target,handler);

p();	//"I am the proxy"

//运行了Proxy实例中定义的apply方法

```

**has()**

has方法用来设置in运算符时的操作，可以隐藏某些属性不被in发现

```js
var proxy = new Proxy(target,{
	has:function(target,key){
		if(key[0] === "_"){
			return false;
		}
		return key in target;
	}
})


//将以'_'开始的属性名隐藏(返回false)

```

**construct()**

construct方法用来拦截new操作，返回必须为对象

```js
//target为构造函数
var proxy = new Proxy(target,{
	construct:function(target,args){
		return {
			value:args
		}
	}
})

new proxy(10);	//{value:10}

```

**deleteProperty()**

deleteProperty方法用来拦截delete操作

```js
var proxy = new Proxy({a:2},{
	deleteProperty:function(target,key){
		if(key in target){
			delete target.key;
			console.log("success");
		}else{
			throw new Error("No that property");
		}
	}
})

delete proxy.a;	// success

delete proxy.b;	//Error:No that property

```

**enumerate()**

enumerate方法用于拦截for...in循环，注意与has方法区分。has方法拦截的是in操作符。

```js
var proxy = new Proxy(target,{
	enumerate:function(target){
		return Object.keys(target).filter(key=>key[0]!=='_');
	}	
})

将以'_'开头的属性隐藏起来，避免被for...in遍历到。

```

**getOwnPropertyDescriptor()**

拦截Object.getOwnPropertyDescriptor

**getProrotypeOf()**

拦截Object.getPeototypeOf()运算符

**ownKeys()**

拦截Object.keys()操作

**preventExtensions()**

拦截Object.preventExtensions()

**setPrototypeOf()**

拦截Object.setPrototypeOf()方法



### Promise