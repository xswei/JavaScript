### 创建型设计模式

- 简单工厂模式
- 工厂方法模式
- 抽象工厂模式
- 建造者模式
- 原型模式
- 单例模式

### 简单工厂模式

简单工厂模式(Simple Factory),又叫静态工厂方法，由一个工厂对象决定创建某一种实例，主要用来创建同一类实例。

```js

var factory = function(args){	//工厂函数，根据type类型选择创建某一个类的实例
	var o = new Object();
	
	//所有实例共有的方法
	o.func = function(){
		//...
	}

	//指定不同的方法
	if(){
		o.func1 = function(){
			//...
		}
	}else{
		o.func2 = function(){
			//...
		}
	}
	return o;
}

var obj = factory(args)

```

### 工厂方法模式

工厂方法模式(Factory Methods),简单工厂模式只适合创建单一类型的实例，工厂方法模式将实际创建对象的工作推迟到子类中。

```js

//安全模式的工厂函数(通过判断this来判断是否使用new关键字)

var Factory = function(type,content){
	if(this instanceof Factory){	//如果使用new关键字，则查找原型链并创建对象
		var s = new this[type](content);
		return s;
	}else{
		return new Factory(type,content);	//如果没有使用new关键字
	}
}

//在工厂原型中创建所有类型数据的基类，添加时只需要修改原型即可。

Factory.prototype = {
	Java:function(content){
		//...
	},
	JavaScript:function(content){
		//...
	},
	php:function(content){
		//...
	}
}

``` 

### 抽象工厂模式

抽象工厂模式(Abstract Factory)

抽象类:是一种声明但是不能直接使用的类

```js

//抽象工厂方法
var AbstractFactory = function(subType,SuperType){

	if(type abstracFactory[subType] === 'function'){	
		//如果原型链上存在对应的抽象类

		//缓存类
		function F(){};
		//继承父类的方法和属性
		F.prototype = new AbstractFactory(superType)();

		//子类的构造器指向子类
		subType.constructor = subType;

		//子类的原型指向父类
		subType.prototype = new F();
	}else{
		throw new Error("不存在对应的抽象类")
	}
}

//Car抽象类
AbstractFactory.Car = function(){
	this.type = "car";
}
AbstractFactory.Car.prototype = {
	getPrice:function(){},
	getSpeed:function(){}
}

//Bus抽象类
AbstractFactory.Bus = function(){
	this.type = "bus";
}
AbstractFactory.Bus.prototype = {
	getPrice:function(){},
	getSpeed:function(){}
}
```

### 建造者模式

建造者模式(Builder)将一个复杂对象构建层与表示层分离，同样的构建过程采用不同的表示

```js

//三个类可以对参数进行解析

var Human = function(){...};
var Name = function(name){...};
var Work = function(work){...};

//在创建person时分别实例化了三个对象，然后将三个对象组合返回
var Person = function(name,work){
	var _person = new Human();
	_person.name = new Name(name);	
	_person.work = new Work(work);
	return _person;
}

```

### 原型模式

原型模式(Prototype)用原型实例指向创建对象的类，可以使得新创建的实例共享原型对象的属性以及方法

```js
var Person = function(name){
	this.name = name;
}


Person.prototype = {
	sayHi:function(){},
	run:function(){}
}

```

### 单例模式

单例模式(Singleton)只允许实例化一次的对象类，常用来定义命名空间，用来管理代码库的各个模块

```js
var A = {
	Util:{
		func1:function(){},
		func2:function(){}
	},
	Tool:{

	},
	Ajax:{

	}
}

```
在使用时候可以根据命名空间区分不同的方法避免命名冲突