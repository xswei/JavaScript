### 正则和数值的扩展

- 正则
- 数值

### 正则扩展



### 数值的扩展

#### 二进制和八进制新写法

二进制和八进制分别使用前缀0b(0B)和0o(0O)表示

```js

0b111110111 === 503 //true
0o767 === 503 // true

```

#### Number.isFinite(), Number.isNaN()

用来检查Infinite和NaN两个特殊值

Number.isFinite()用来检查一个数值是否非无穷

```js
Number.isFinite(12);	//true
Number.isFinite(NaN);	//false
Number.isFinite(Infinity);	//false
Number.isFinite("foo");	//false

```

Number.isNaN()用来判断一个值是否是NaN

```js

Number.isNaN(NaN);	//true
Number.isNaN(15);	//false
Number.isNaN("15");	//false
Number.isNaN(9/NaN);	//true

```

#### Number.parseInt(), Number.parseFloat()

将parseInt()和parseFloat()移植到了Number对象上，不再当成全局的方法

#### Number.isInteger()

判读一个数是否为整数，要注意在JavaScript内部整数和浮点数使用同样的存储方法，所以3和3.0被视为同一个值

```js

Number.isIntege(25);	//true
Number.isIntege(25.0);	//true
Number.isIntege(25.1);	//false
Number.isIntege("15");	//false
Number.isIntege(true);	//false

```

#### Number.EPSILON

新增的一个极小的常量，因为浮点数计算的误差，如果两个浮点数误差小于这个值，则可以认为两个浮点数相等

#### 安全整数和Number.isSafeInteger()

JavaScript可以准确表示的整数范围在-2^{53}到2^{53}之间，超过这个范围的就无法精确表示

ES6引入了Number.MAX_SAFE_INTEGER和Number.MIN_SAFE_INTEGER两个常量用来表示这个范围的上下限。

Number.isSafeInteger()方法用来检测一个整数是否在这个范围内:

```js

Number.isSafeInteger("a");	//false
Number.isSafeInteger(null);	//false
Number.isSafeInteger(NaN);	//false
Number.isSafeInteger(Infinity);	//false
Number.isSafeInteger(-Infinity);	//false

Number.isSafeInteger(3);	//true
Number.isSafeInteger(1.3);	//false

Number.isSafeInteger(Number.MAX_SAFE_INTEGER);	//true
Number.isSafeInteger(Number.MAX_SAFE_INTEGER+1);	//false

```

#### Math对象的扩展

- Math.trunc():用以除去小数的小数部分，只留下整数部分
- Math.sign():判断一个数是正数，还是负数还是0
- Math.cbrt():计算一个数的立方根
- Math.clz32():返回一个数的32位无符号整数表示形式前有多少个0
- Math.imul():返回两个数以32位带符号整数形式相乘的结果，返回值也是一个32位带符号的整数
- Math.fround():返回一个数的单精度浮点数表示
- Math.hypot():返回所有参数的平方和的平方根

```js
Math.trunc(4.1);	//4
Math.trunc(-4.1);	//-4

Math.sign(10);	//1
Math.sign(0);	//0
Math.sign(-10);	//-1

Math.cbrt(-1);	//-1
Math.cbrt(0);	//0
Math.cbrt(8);	//2

Math.clz32(0);	//32
Math.clz32(1);	//31
Math.clz32(0b0100000000000000000000000000000);	//0

Math.imul(2,4);	//8
Math.imul(2,-4);	//-8

Math.fround(0);	//0
Math.fround(1);	//1
Math.fround(1.337);	//1.3370000123977661

Math.hypot(3,4);	//5(3*3+4*4=5*5)
Math.hypot(3,4,5);	//7.0710678118654755

```

#### 对数方法

- Math.expm1():返回e^x-1，即Math.exp(x)-1
- Math.log1p():返回ln(x+1)，即Math.log(1+x),如果x小于-1则返回NaN
- Math.log10():返回以10为底的x的对数，如果x小于0则返回NaN
- Math.log2():返回以2为底的x的对数，如果x小于0则返回NaN

#### 三角函数方法

- Math.sinh(x):返回x的双曲正弦 
- Math.cosh(x):返回x的双曲余弦 
- Math.tanh(x):返回x的双曲正切 
- Math.asinh(x):返回x的反双曲正弦 
- Math.acosh(x):返回x的反双曲余弦 
- Math.atanh(x):返回x的反双曲正切 

#### 指数运算符号

``**``运算

```js
2**2 //4
2**3 //8

```

`**=`运算

```js
let a = 2;
a **= 3;	//a=8

let b = 3;
b **= 3;	//b=b*b*b

```
