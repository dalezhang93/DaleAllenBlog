typeof 检查基础数据类型
instanceof 检查引用数据类型
toString.call() 基础数据类型和引用数据类型均可检验

基本数据类型: Undefined、Null、Boolean、Number(NaN属于 Number)
引用数据类型: Object、Array、Function 和 String
其中 Array Function 都属于 Object 子类型
symbol 符号类型为 ES6新增类型

## undefined
`undefined` 是未初始化的值

用 `==` 判断,因为`null`也可以成立
用 `===` 严格判断,若没有声明变量,会抛出错误
用 `typeof` 判断,即使没有声明变量,也是 `undefined`
`void 0` 等于`undefined`,用`void 0`可以防止`undefined`被覆盖
 - 不是保留关键字,在低版本 IE 中'undefined'可以被覆盖
 - 可以省不少字节码

## null

typeof null 为 object
toString.call(null)为 [object Null]
最方便的是与本身比较 null == null

## 数组
instanceof 运算符用来检测 constructor.prototype 是否存在于参数 object 的原型链上(instanceof 应用于引用类型的判断)
使用 toString.call(object) 判断数据类型(该方法不区分基本类型和引用类型)
Array.isArray 是 ES5原生方法判断是否是数组
```
_.isArray = Array.isArray || function(obj) {
  return toString.call(obj) === '[object Array]';
};
```

### 类数组
var arr = Array.prototype.slice.call( arguments );
ES6提供 Array.from()

## 数字

### +0,-0

### 整数

ES6提供 Number.isInteger(方法)
typeof num == "number" && num % 1 == 0;

### NaN
NaN 说明某些算术运算（如求负数的平方根）的结果不是数字,"不是数字",是唯一不等于自己的 number 类型,但是`new Number(NaN)`等于自身
使用`toString.call()`判断是'[object Number]'

isNaN() 函数用于检查其参数是否是非数字值
  - 如果参数值为 NaN 或字符串、对象、undefined等非数字值则返回 true,(中间做了强制转换成 Number类型), 否则返回 false.
  - 空字符串''和空格' ',当做0处理,返回 false
  - 字符串是全数字,返回 false
  - 判断字符串是全数字,`isNaN(parseInt())`

Number.isNaN() ES6引入,中间不做强制转换,只有是 NaN 的时候才返回 true
```
_.isNaN = function(obj) {
  return _.isNumber(obj) && isNaN(obj);
};
// 首先 NaN 是 number 类型
// 只有 NaN 和 new Number(NaN) 能通过这个函数
// 能通过 Number.isNaN 函数的只有 NaN
```

## DOM 元素
```
  _.isElement = function(obj) {
    // 确保 obj 不是 null
    // 并且 obj.nodeType === 1
    // 返回使用 !! 保证返回布尔值
    return !!(obj && obj.nodeType === 1);
  };
```

## 布尔

用 if 判断空字符串`''`是`false`,任何值不为 undefined或者 null的对象, 包括值为false的Boolean对象, 在条件语句中,其值都将作为true来判断

new Boolean()是创建一个布尔对象,如果参数是 0, -0,  null, false, NaN, undefined, 或者空字符串 (""),生成的Boolean对象的值为false. 其他任何值,包括**任何对象**或者字符串"false", 都会创建一个值为true的Boolean对象
  - 包括 值为 true 的布尔对象
  - 值为 false 的布尔对象,if 判断为 true
因此使用 Boolean()方法将非布尔值转换为布尔值

```
_.isBoolean = function(obj) {
    return obj === true || obj === false || toString.call(obj) === '[object Boolean]';
  };
```

## 参数

toString.call()
但是IE<9 返回[object Object]
用是否含有 callee 属性来判断
Object.prototype.call(obj, 'callee')

## object 和 function
function 使用 toString.call()
object

```
_.isObject = function(obj) {
  var type = typeof obj;
  return type === 'function' || type === 'object' && !!obj;
};
// !!obj 是去除 null
```
