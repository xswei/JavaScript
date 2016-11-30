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

Promise是一个可以传递异步操作的对象。有以下特点:

- 对象的状态不受外界影响。Promise对象代表一个异步操作，有3种状态:Pending(进行中),Resolved(已完成)和Rejected(已失败)。

- 对象的状态一旦改变就不会再次被其他手段改变，状态的改变有两种可能:从Pending变成Resolved和从Pending变成Rejected。

#### 基本用法

Promise是一个对象，在使用前首先对其进行实例化:

```js
var promise = new Promise(function(resolve,reject){
	//...do something

	if(//操作成功){
		resolve(value);
	}else{
		reject(error);
	}
})

```
Promise构造函数接受一个函数为参数，这个函数的两个参数分别为resolve和reject。这两个参数也为函数，不需要自己指定。

resolve函数的作用是当异步操作执行成功时候将操作结果作为参数传递出去，reject的作用是当异步操作失败时候将失败信息作为参数传递出去。

在实例化Promise对象之后，可以用then方法分别指定成功或失败后的回调函数:

```js
promise.then(function(value){
	//成功时执行
},function(error){
	//失败时执行
})

```

下面是一个异步加载图片的例子:

```js
function loadImage(url){
	return new Promise(function(resolve,reject){
		var image = new Image();
		image.onload = function(){
			resolve(image);
		}
		image.onerror = function(){
			reject(new Error("Couldn't load image at "+url));
		}
		image.src = url;
	})
}

loadImage("./img.png").then((image)=>{
	console.log(image)
	document.body.append(image);	//如果成功将其追加到body中
},(err)=>{
	console.log(err);				//失败的话打印失败信息
})

```

在上述例子中，resolve和reject函数的参数都为一个确切的值，除此之外resolve的值还可以为另一个Peomise实例，这样就形成了多个异步操作之间的衔接。

```js
var p1 = new Promise((resolve,reject)=>{
	//...
})
var p2 = new Promise((resolve,reject)=>{
	//...
	resolve(p1);
})

```

在上述代码中,p2的resolve函数参数为p1,也就是p2的异步操作结果返回另一个异步操作。

此时p1的状态决定了p2的状态，当p1为Pending时,p2的回调就会等待p1的结果。如果p1的状态改为Resolved则p2的回调就会立即执行。如果p1的状态改为Rejected则p2也会变为Rejected。

#### Promise.prototype.then()

用来为Promise实例添加状态改变时的回调函数。

#### Promise.prototype.catch()

可以看做是.then(null,rejection)的别名，用以指定发生错误时的回调函数。

```js

promise.then((value)=>{
	//success
}).catch((err)=>{
	//failed
})

```

#### Promise.all()

用来将多个Promise实例包装成一个Promise实例

```js
var p = Promise.all([p1,p2,p3]);

```

此时p的状态就由p1,p2,p3共同决定，只有p1,p2,p3的状态都为Resolve时p的状态才为Resolve.

#### Promise.race()

也用来将多个Promise实例包装成一个Promise实例，与Promise.all()不同的是p1,p2,p3中只要有一个状态改变则包装后的实例状态就跟着改变

#### Promise.resolve()

将现有的对象转为Promise对象。

比如:

```js
var promise = Promise.resolve($.ajax("./data.json"))

```

#### done()和finally()

done()方法也可以链式使用。不管使用then方法还是catch方法，本身then方法和catch方法也可能抛出错误，这些错误没有办法被捕捉到，因为Promise内部的错误不会被冒泡到全局，因此在链式回调的末端需要使用done()方法可以保证出错时候被捕捉到。

```js
promise.then(...)
	.catch(...)
	.then(...)
	.catch(...)
	.done()

```

finally方法用于指定不管Promise对象的最后状态如何都会执行的操作。与done不同的是finally接受一个普通的函数为参数，无论最后结果如何都会执行。
