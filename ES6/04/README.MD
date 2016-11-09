### 数组和对象的扩展

### 数组的扩展

#### Array.from()

Array.from()将类数组括可迭代对象(包括set和map)转为真正的数组

```js
let arrayLike={
	"0":"a",
	"1":"b",
	"2":"c",
	length:3
}

ES5: 
var arr1 = [].slice.call(arrayLike);	//["a","b","c"]

ES6: 
var arr2 = Array.from(arrayLike);	//["a","b","c"]

```

Array.from()还接受第二个参数，第二个参数是一个方法，类似map一样对每个元素进行处理:

```js

Array.from([1,2,3],x=>x*x);	//[1,4,9]

```

#### Array.of()

Array.of()用于将一组值转换为数组

```js
Array.of(3,11,8);	//[3,11,8]

```

#### 数组实例的copyWithin()

copyWithin()方法用以将指定位置的成员负值到其他位置，覆盖目标位置的值，会修改原数组


Array.prototype.copyWithin(target,source=0,end=this.length):

- target,必须,目标位置
- start,可选,从该位置开始读取
- end,可选,到当前位置结束,默认等于数组的长度

```js
[1,2,3,4,5].copyWithin(0,3);
//原数组变为[4,2,3,4,5]

```

#### 数组实例的find()和findIndex()

- find()方法用来找出第一个符合条件的数组成员
- findIndex()用来找出第一个符合条件的成员索引

```js
[1,5,10,15].find(function(value,index,arr){
	return value>9;
});	//10

[1,5,10,15].findIndex(function(value,index,arr){
	return value>9;
});	//2

```

#### 数组实例的fill()

使用给定的值填充数组,初始化数组时很方便

```js
["a","b","c"].fill(7);	//[7,7,7]

new Array(3).fill(0);	//[0,0,0]

```

#### 数组实例的entries(),keys()和values()

都用于遍历数组，都返回一个遍历器对象。可以使用for...of循环

```js

for(let index of ["a","b"].keys(){
	console.log(index);
})
//0,1

for(let values of ["a","b"].values(){
	console.log(values)
})
//"a","b"

for(let [index,value] of ["a","b"].entries(){
	console.log(index+" "+value);	
})
//"0 a","1 b"

```

#### 数组实例的includes()方法

是否包含某个值,可以指定第二个参数用以表示开始搜索的位置


### 对象的扩展

####属性的简洁表达

```js
var foo = "bar";
var baz = {foo};
//baz:{foo:"bar"}

function f(x,y){
	return {x,y}
}
等价于:
function f(x,y){
	retrn {x:x,y:y}
}

var o = {
	method(){
		return "hello"
	}
}
等价于:
var o = {
	method function(){
		reutrn "hello"
	}
}
```
CommonJS规范的简写:

```js

var ms{};
function f1(key){
	return key in ms?ms[key]:null
}
function f2(key,value){
	ms[key] = value
}
function f3(){
	ms = {};
}
modules.exports = {f1,f2,f3}
等价于:
modules.exports = {
	f1:f1,
	f2:f2,
	f3:f3
}

```

#### 属性名表达式

ES6允许将表达式作为属性名:

```js
let propKey = "foo";

let obj={
	[propKey]:"true",
	["a"+"bc"]:123
}
//obj:{foo:true,"abc":123}

```

#### 方法的name属性

函数的name属性返回函数名

```js
var p ={
	sayHi:function(){
		console.log("hi");
	}
}

p.sayHi.name;	//sayHi

```

#### Object.is()

用来比较两个值是否严格相等，与严格比较符(===)基本一致

#### Object.assign()

Object.assign(target,source1,source2,...)将源对象的所有可枚举属性复制到目标对象

如果源对象与目标对象有相同的属性，或者多个源对象有相同的属性，则后面的覆盖前面的。

对于嵌套的对象，Object.assign会直接替换：

```js
var target = {
	a:{
		b:"c",
		d:"e"
	}
}
var source = {
	a:{
		b:"hello"
	}
}
Object.assign(target,source);
//{a:{b:"hello"}}

```

Object.assign方法可以用来为对象添加属性和方法，也可以用来克隆对象和合并多个对象

#### 属性的可枚举性

每个属性都有一个属性描述符(Descriptor)用以控制该属性的行为，可以使用Object.getOwnPropertyDescriptor()来获取对象的描述。

ES5中有以下3个方法会忽略enumerable为false的属性:

- for...in
- Object.keys()
- JSON.stringify()

ES6新增了两个操作，也会忽略enumerable为false的属性:

- Object.assign()
- Reflect.enumerable():返回所有for...in会遍历到的属性

#### 属性的遍历

ES6有6种方法可以遍历属性:

- for...in
- Object.keys(obj)
- Object.getOwnPropertyNames(obj)
- Object.getOwnPropertySymbols(obj)
- Reflect.ownKeys(obj)
- Reflect.enumerable(obj)

#### __proto__属性,Object.setPropertyOf(),Object.getPrototypeOf()

__proto__属性用来读取或设置对象的prototype对象

Object.setPropertyOf()用来设置属性的prototype对象

Object.getPropertyOf()用来获取属性的prototype对象