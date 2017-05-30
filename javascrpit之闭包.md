## 什么是闭包

> 当函数可以记住并访问所在的`词法作用域`,即使函数是在当前词法作用域之外执行,这时就产生了闭包

当函数执行完后,会销毁函数内部的作用域,这被成为`垃圾回收机制`.而闭包依然保存了对作用域的引用,避免被`回收`.需要注意的是闭包的滥用会导致内存的泄露.

```
function foo(){
  var id = 1;
  function bar(){
    console.log(id);
  }
  return bar;
}
var foo1 = foo();
foo1(); // 1
```
`foo`函数执行后,将`bar`函数本身返回并赋值给`foo1`变量,此时`foo`函数内部作用域并没有被垃圾回收,因为`foo1`保留了对其作用域的引用,这就是`闭包`.

## 使用闭包

### 循环和闭包

看这一经典面试题:
```
for(var i=0; i <= 5; i++){
  setTimeout(function(){
      console.log(i);
    },i*1000);
}
```
预期的返回结果是依次输出 0 1 2 3 4 5,而事实上返回结果是依次输出每秒一次 6 6 6 6 6 6
事实上延迟函数的回调是在循环结束后才执行的.每次循环过程中并没有创造新的词法作用域,传入回调函数的参数 i在循环后为为6,这里只有一个变量 i, 没有其他副本.
 我们可以使用立即执行函数调用来创造作用域,并将参数 i 传入:
 ```
 for(var i=0; i <= 5; i++){
 	(function(i){
 		setTimeout(function(){
 			console.log(i);
 		},i*1000)
 	})(i) // 如果没有将参数 i 传入,也只是创造一个空的作用域
 }
```
或者使用`let`创造块级作用域:
```
for(let i=0; i <= 5; i++){
	setTimeout(function(){
		console.log(i);
	},i*1000)
}

```

### 模块

在面向对象编程中,我们通过构造器`new`一个新的对象,而 js 可以通过闭包实现类似效果,这种模式被称为`模块`.以计时器为例:
```
function createCounter() {
	var counter = 0;
	function increase() {
		counter++;
		console.log(counter);
	}
	function decrease() {
		counter--;
		console.log(counter);
	}
	return {
		increase:increase,
		decrease:decrease
	}; // 创造闭包,返回 increase 和 decrease 方法,该方法均保留了 createCounter()函数的作用域
}

var counter1 = createCounter();
counter1.increase(); // 1
counter1.increase(); // 2
counter1.increase(); // 3
counter1.decrease(); // 2

var counter2 = createCounter(); // 又创建了新的计数器,新的实例
counter2.increase(); // 1
```

`模块`必须有一个封闭函数,该函数至少被调用一次,每次调用产生都会创建新的模块实例.并且函数必须返回至少一个内部函数或者包含内部函数的对象,从而形成`闭包`
当只需要一个实例时,通过立即执行函数实现单例模式

```
var counter = (function createCounter() {
	var counter = 0;
	function increase() {
		counter++;
	}
	return {increase:increase}
})();
```
