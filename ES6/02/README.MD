### unicode编码和字符串新特性

- unicode编码
- 字符串新特性


### unicode编码

JavaScript使用unicode编码，可以识别\u0000到\uFFFF之间的字符，如果超出这个字符则必须要使用两个双字节的形式表达。

比如如果一个字符超出0xFFFF，在ES5中会被截断:

```js
\u20BB7会被理解成\u20BB+7

```

在ES6中支持对超出0xFFFF的字符的解析，使用{}括起来即可:

```js

\u{20BB7};


"\u{1F680}" === "\uD83D\uDE80";	//true,表示大括号标记法与四字节的UTF-16编码等价

```

### 字符串新特性

#### codePointAt

在JavaScript内部，字符以UTF-16格式存储，每个字符为2个字节，对于使用4个字节存储的字符会误认为其是2个字节。

codePointAt是string.charCodeAt的改进版，charCodeAt方法不能正确识别4个字节的字符。

```js

//ES5
var s = "吉"
s.length;	//2
s.charCodeAt(0);	//55362,只读取了两个字节
s.charCodeAt(1);	//57271

//ES6
var s = "吉a"
s.length;	//2
s.codePointAt(0);	//134071,正确读取了4个字节
s.codePointAt(1);	//57271
s.codePointAt(2);	//97

```

可以用来判断你一个字符是由几个字符组成，判断码点与0xFFFF的关系。

#### String.fromCodePoint

ES5中的String.fromCharCode方法可以返回指定码点的字符。但是不能识别4个字节的字符。

```js

//ES5

String.fromCharCode(0x20BB7);	//"(不知道是啥)"

//ES6

String.fromCodePoint(0x20BB7);	//"吉"

```

如果String.fromCharPoint()有多个参数则将其对应的字符拼接起来返回

#### 字符串遍历

ES6为字符串添加了遍历接口，可以使用for..of遍历，最大的优点是可以识别码点大于0xFFFF的字符

```js

for(let codePoint of "foo"){
	console.log(codePoint);
}
//"f" "o" "o"

```

#### at()

ES5提供了charAt()方法用来返回字符串指定位置的字符，但是不能识别4个字节的字符。

ES6提供了at()方法可以正确的识别4个字节的字符

```js

//ES5
"abc".charAt(0);	//"a"
"吉利".charAt(0);	//"\uD842"

//ES6
"abc".at(0);	//"a"
"吉".at(0);	//"吉"

```

#### includes,startsWith,endsWith

在ES5中只能通过indexOf方法来判断字符串中是否包含另一个字符串

ES6提供了三种方法,都返回布尔值:

- includes()
- startsWith()
- endsWith()

#### repeat(n)方法

将一个字符串重复n次:

```js

"hello".repeat(2);	//"hellohello"

```

如果n为小数则取整，如果n为负值或其他非法值则出错。

#### padStart(),padEnd()

ES6推出的字符串自动补全长度的功能。

```js
"x".padStart(5,"ab");	//"ababx"
"x".padStart(4,"ab");	//"abax"

"x".padEnd(5,"ab");		//"xabab"
"x".padEnd(4,"ab");		//"xaba"

```

如果指定的长度小于等于原字符串，则什么都不做，如果省略第二个字符串，则默认使用空格补齐:

```js
"x".padStart(5);	//"    x"

```

#### 模板字符串

模板字符串使用`...`形式，要使用(`)引起来以区分普通字符串和模板字符串。

模板字符串中的变量使用${变量名}表示。

```js
var name = "wxs",	
	age = "24";
`hello ${name},your age is ${age}`

```

如果字符串中包含(`)则要使用(`\`)来转义

在${}中可以放置任意的JavaScript表达式，比如运算和对象属性等。还能调用函数:

```js
function f(){
	return "world";
}

`hello ${f()}`
```

####　String.raw()

ES6添加了String的raw方法，用来返回一个模板被替换的字符串。

```js

String.raw(`Hi\n${2+3}!`);

//"Hi\\n5!",注意转义符前添加了一个\

如果原字符串中的转义符已经被转义，则不做处理

String.raw(`Hi\\n`)
//"Hi\\n"

```
