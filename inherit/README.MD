### JavaScript中的继承

JavaScript中的每个类都有三个部分内容:

- 构造函数内部提供实例化对象复制用
- 构造函数外部通过.语法添加的
- 原型中的

面向对象程序设计中有继承的概念，而在JavaScript中继承是通过原型链实现的。

实现继承的方式有如下几种:

- 类式继承
- 构造函数继承
- 组合继承
- 原型式继承
- 寄生式继承
- 寄生组合式继承

### 1. 类式继承

类式继承是最简单的一种继承实现方式。

```js

//父类构造函数
function SuperClass(){
	this.superValue = true;
}

//父类方法
SuperClass.prototype.getSuperValue = function(){
	return this.superValue;
}

//子类构造函数
function SubClass(){
	this.subValue = false;
}

//继承父类
SubClass.prototype = new SuperClass();

//子类方法
SubClass.protorype.getSubValue = function(){
	return this.subValue;
}

```

类式继承通过将父类的实例赋值给子类的构造函数的原型(prototype属性)，类的原型对象就是为了为类的原型添加共有方法，但是类不能直接访问这些属性和方法，只能通过原型prototype来访问，在实例化父类时，新创建的对象复制了父类构造函数内的属性和方法并将其原型__proto__指向了父类的原型对象。而将这个新创建的对象赋值给子类的原型，则子类的原型就可以访问到父类的原型属性和方法。

所以子类不仅可以访问父类构造函数中的属性和方法，还可以访问父类原型上的属性和方法。

类式继承有两个缺点:

- 子类通过prototype对父类进行实例化，所以如果父类中的属性是引用类型时，某个子类对这个属性进行更改，会影响到其他的子类
- 子类的实现是通过原型pototype对父类进行实例化实现的，在创建父类时，无法向父类传递参数，因而在实例化父类时也无法对父类构造函数内的属性进行初始化

### 2. 构造函数继承

```js
//父类构造函数
function SuperClass(id){
	this.books = ["c","c++","java"];
	this.id = id;
}
//父类原型方法
SuperClass.prototype.showBooks = function(){
	console.log(this.books);
}

//子类构造函数
function SubClass(id){
	SuperClass.call(this,id);	
}

```
SuperClass.call(this,id)是构造函数继承的精华，call方法可以改变函数的作用环境，因此在子类中可以将子类中的变量在父类的构造函数中执行一遍，在父类中通过this绑定属性，因此子类也就继承了父类的共有属性。要注意子类可继承的属性和方法仅仅是构造函数中的属性和方法。

构造函数继承没有涉及到prototype,所以父类的原型方法不会被子类继承，如果将属性或方法放在构造函数中来实现继承，那么每个子类都会有一个单独的属性或方法，不符合代码服用的原则。


### 3. 组合继承

类式继承和构造函数继承各有利弊，如果将两种继承方式结合，则就成了组合继承。

```js
//父类构造函数
function SuperClass(name){
	this.name = name;
	this.books = ["c","c++","java"];
}
//父类原型方法
SuperClass.prototype.getName = function(){
	return this.name;
}
//子类构造函数
function SubClass(name,time){
	SuperClass.call(this,name);		//构造函数继承
	this.time = time;
}
//类式继承
SubClass.prototype = new SuperClass();

//子类方法
SubClass.prototype.getTime = function(){
	return this.time;
}
```

组合继承融合了类式继承和构造函数继承的优点，但是也有一个明显的缺点，就是在实例化子类时父类的构造函数会执行两次。

### 4. 原型式继承

原型式继承的观点是可以借助prototype根据已有的对象创建一个新对象，而不必同时穿件新的自定义对象类型。

```js
//原型式继承
function inheritObject(o){
	function F(){};		//声明一个过渡函数对象
	F.prototype = o;	//继承父类
	return new F();		//返回过渡对象的一个实例
}	

```

这种继承是类式继承的一个封装，其中的过渡对象相当于类式继承中的子类，由于过渡类的构造函数中无内容，因此开销比较小。

原型继承的缺点与类式继承一样，父类的引用类型属性也会被公用。

### 5. 寄生式继承

```js

//父类或基对象
var book = {
	name:"js book",
	alikeBook:["css","html"]
}

//继承并返回子类
function createBook(obj){
	var o = new inheritObject(obj);

	o.getName = function(){
		return this.name;
	}
	return o;
}

var book1 = createBook(book);	//创建子类，以book为父类
```

寄生式继承就是对原型继承进行了二次封装，在封装过程中可以对继承的对象进行拓展，可以为子类添加新的属性和方法。

### 6. 寄生组合式继承

在将类式继承与构造函数继承组合使用时有一个问题就是子类不是父类的实例，而子类的原型是父类的实例，而寄生式组合继承则将寄生式继承以及构造函数继承组合使用。

```js
//寄生组合式继承
function inheritPrototype(subClass,superClass){
	//复制父类的原型的副本
	var p = inheritObject(superClass.prototype);
	p.constructor = subClass;	//将副本的构造函数指向子类的构造函数
	subClass.prototype = p;		//设置子类的原型
}

```

寄生组合式继承中，修改了父类的原型副本的构造函数为子类的构造函数，但是保留了副本的其他属性，这是因为我们需要的是父类的原型，而不再需要调用父类的构造函数。而父类的构造函数在inheritObject中已经调用，属性和方法已经被初始化。

### 多继承和多态

#### 多继承

JavaScript的继承基于原型链，但是只有一条原型链，因此JavaScript中的多继承要通过其他方法实现。

```js

function extent(target,source){
	for(var property in source){
		target[property] = source[property];
	}
	return target;
}

```

多继承就是一个属性的复制过程，但是这个复制是浅复制，只能复制值类型的属性，而不能复制引用类型的属性。

当然也可以同时对多个对象进行集成，只需要将上述方法的参数设为不固定，在函数内部判断参数个数并添加一层循环依次复制即可。

#### 多态

多态就是一种方法的不同调用方式，在JavaScript中多态的实现是通过对参数进行判断，执行不同的操作实现的。