# javascript for 循环

标签（空格分隔）： javascript

---

## for(;;;) ##
最常见的 for 循环写法如下:
```
for (语句 1; 语句 2; 语句 3)
{
    // do something
}
```

 - 语句1,经常用来初始化变量,可以放在for 循环中,也可以放在for 循环之前.在`javascript`中声明变量经常使用 `var `,当 `for`循环结束后声明的变量仍然存在;当使用 `let`声明变量,由于 for 循环属于块作用域, 循环之后不能再使用声明的变量.
```
var array = [1,2,3,4,5,6,7,8];
// 使用 var 初始化变量
for (var i = 0; i < array.length; i++) {
	// do nothing
}
console.log(i);// 8
// 使用 let 初始化变量
for (let j = 0; j < array.length; j++) {
	// do nothing
}
console.log(j);// j is not defined
```
 - 语句2中,经常使用 `array.length`,但是在for循环中,会被重复执行.
```
var array = [1,2,3,4,5,6,7,8];
function fun1(array) {
	console.log('test');
	return array.length;
}
for (let i = 0; i < fun1(array); i++) {
	// do nothing
}
// 观察到打印了多次, fun1()函数在循环中多次执行:
// test
// test
// ...

// 推荐将长度的获取放到语句1中
for (let i = 0,j = fun1(array); i < j; i++) {
	// do nothing
}
```

## for...in ##
```
// 遍历 console 对象
for (let index in console) {
  console.log(index);
}
```
优点:写法简单,适合遍历对象
缺点:
 - index是字符串而不是数值,当遍历数组时会造成困扰
 - 某些情况下甚至不按顺序遍历
 - 遍历到自定义属性甚至原型属性,存在性能问题

**不推荐使用**

## forEach ##
forEach函数是在 ES5中推出的函数,用来遍历数组.
缺点:
- 存在兼容性,在 IE 中无法使用;
- 无法遍历对象
- 无法使用break和continue关键字,return实际充当 continue 的角色.
```
var array = ["a", "b", "c"];
array.forEach(function(value,index,arry) {
    if(index == 0){
		return;
	}
    console.log(value); // b  c
});
```
## JQuery ##
在 Jquery 中提供了$.each() 和$().each()方法用于遍历对象和数组
一般$('selector').each()用来对DOM进行遍历
$.each()用来遍历执行对象和方法
优点:写法简单
缺点:
- 内部使用call和apply方法,比浏览器原生方法要慢.
- 不支持set和 map
```
// 遍历 console对象
// 遍历对象时函数第一个参数 key 为键值;遍历数组时为 index 索引,索引从1开始
$.each(console,function(key,element){
    // do something
})

// 不支持continue,和 break关键字,使用 return替代
$.each([1,2,3],function(index,element){
    if (index == 2){return;   // continue}
    if (index == 3){return false;   // break}
})
```

## underscore ##
`underscore`库像 JQuery 一样将数据封装到一个自定义对象中,并且优先原生方法.
对于遍历其提供了一个each()方法
源码如下:
```
_.each = _.forEach = function(obj, iteratee, context) {
    iteratee = optimizeCb(iteratee, context);
    var i, length;
    if (isArrayLike(obj)) {
      for (i = 0, length = obj.length; i < length; i++) {
        iteratee(obj[i], i, obj);
      }
    } else {
      var keys = _.keys(obj);
      for (i = 0, length = keys.length; i < length; i++) {
        iteratee(obj[keys[i]], keys[i], obj);
      }
    }
    return obj;
  };
```

观察到该`each`方法
- 支持数组及其他类数组和对象的遍历
- 不同于 JQuery 的 bind绑定,使用迭代进行遍历
缺点:
- 同样不支持Set和 Map
```
// 遍历 console对象,underscore 使用 _ 
// 遍历对象(element, index, list)
_.each(console,function(element, index, list){
    // do something
})
// 遍历数组(value, key, list)
```

## for...of ##
在 ES6标准中推出的方法.写法可以像 for...in 一样简洁,不仅可以遍历数组也可以遍历对象,实际上可以遍历任何实现了部署了Iterator接口的数据结构,包括 Map和Set.实际上一个数据结构只要部署了Symbol.iterator属性,就可以被遍历


- 相对于 for...in, 并不会遍历数组添加的属性;for...in 读取的键名key,而 for...of 读取的键值value
- 只要部署了Symbol.iterator属性,就可以被遍历.原生支持的有数组,字符串,一些类数组对象,Set,Map
- 可以使用 break跳出循环
```
let obj={a:1,b:2};
let array=[1,2,3,4,5];

array.foo='foo';// 添加属性
// for...in 会遍历添加的属性,遍历索引
for(let i in array){
	console.log(i); // 0 1 2 3 4 foo
}
// for...of 不会遍历添加的属性,遍历键值
for(let i of array){
	console.log(i);// 1 2 3 4 5
}
// 可以使用entries()方法返回一个遍历对象,取得索引
for(let [i,j] of array.entries()){
	console.log(i); // 0 1 2 3 4
}
//遍历字符串,字符串没有entries()方法
let string = 'abcdefg';
for(let i of string){
	console.log(i); // a b c d e f g
}
// 遍历 set,遍历顺序为添加顺序,返回键值;使用entries()方法,对 Set来说键名和键值相同
var set = new Set();
set.add('a').add('b');
for (var i of set){
  console.log(i); // a b
}
// 遍历 map, 遍历顺序为添加顺序,返回值为数组[key,value],支持解构写法;默认就是使用entries();
var map = new Map();
map.set("a", 1).set("b", 2).set("c", 3);
for (var [name, value] of map) {
  console.log(name + ": " + value);
}
// a: 1
// b: 2
// c: 3

```

但是,普通的对象并没有Symbol.iterator属性,无法被遍历;如果想让一个对象可遍历,就让其添加`Symbol.iterator`属性

```
// 为jQuery对象实现遍历
jQuery.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator];
```

也可以使用 `Object.keys`方法将对象的键名生成一个数组,然后遍历这个数组.该方法在IE8中并不支持;或者采用`underscore`的`_.keys`方法达到同样效果