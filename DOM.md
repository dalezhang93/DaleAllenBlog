

<!-- toc orderedList:0 depthFrom:1 depthTo:6 -->

* [HTML](#html)
* [Node 节点](#node-节点)
	* [**常用API**](#常用api)
	* [高效遍历 DOM](#高效遍历-dom)
* [Repaint and reflow](#repaint-and-reflow)
	* [插入大量内容避免重绘和回流](#插入大量内容避免重绘和回流)
	* [style 样式操作](#style-样式操作)
* [DOM事件](#dom事件)

<!-- tocstop -->

## HTML

	- innerHTML:内部HTML，content<br/>；
	- outerHTML:外部HTML，<div>content<br/></div>；
	- innerText:不会返回隐藏元素的文本,受 CSS 样式的影响,它会触发重排(reflow);IE11及以下,不仅会移除当前元素的子节点，而且还会永久性地破坏所有后代文本节点
	- textContent,会获取所有元素的内容,包括 css内容;对比innerText,不会引起重排;兼容性: IE9开始支持
	- outerText:outerText 赋值时会把整个标签一起赋值掉;非标准属性,不要在生产环境中使用!

`jQuery`的`html()`会调用`innerHTML()`来操作,但同时也会 catch 异常，然后用.empty(),.append()来重新操作.这是因为IE8中有些元素的.innerHTML是只读的

## Node 节点

`Node`是一个接口，许多`DOM`类型从这个接口继承,常用如元素节点,文本节点,属性节点.
每个`node`对象均有`nodeValue`,`nodeType`属性:
- `nodeValue`
 	- 元素节点值是 undefined 或 null
 	- 文本节点值是 文本本身
 	- 属性节点值是 属性值,使用 getAttribute()

- 常用`nodeType`
	- 1 Element 元素节点
	- 2 Attr    属性节点
	- 3 Text	  文本节点
	- 4 CDATASection
	- 9 Document DOM 树的根节点
	- 11 DocumentFragment

可以用在遍历`DOM`树时使用节点类型过滤
```
var allChildren = document.getElementById('inhere');
for(var i = 0; i < allChildren.length; i++) {
	if(allChildren[i].nodeType == 1 && allChildren[i].hasAttribute('someattr')) {
		// …
		break;
	}
}
```

`NodeList`是一个节点的集合,具有以下特征:
- **实时性**
由 Node.childNodes 和 document.querySelectorAll 返回,大多数情况下是一个实时的集合,当文档节点变化时,`NodeList`对象也会变化.因此当使用完该对象后,避免保留节点引用是好的编程习惯.在遍历节点集合的时候应避免对`DOM`进行操作影响遍历过程;需要注意的是例外情况:document.querySelectorAll 返回的是静态集合.

- **节点集合与数组**
NodeList 继承自 NodeList.prototype,而数组继承自Array.prototype,因此要想使用数组方法有很多方法:
	- 使用原型链把Array.prototype 上的方法添加到 NodeList.prototype, 但是扩展 DOM对象的原型链是很危险
	- 数组原型方法使用 call将 NodeList 作为this参数传递给原生方法,不保证所有浏览器均兼容
	- 将 Nodelist 转化为数组:Array.prototype.slice.call(NodeList),可以简单使用[].slice.call(NodeList) 兼容性:IE9+

- **HTMLCollection**
	- 很明显`HTMLCollection`只是元素节点的集合,不同的是元素节点集合是实时的,而`Nodelist`不总是实时的
	- `Node.childNodes`返回的是`NodeList`,而`node.children`返回的是`HTMLCollection`


### **常用API**
---
**节点操作**

	- element.hasChildNodes 是否拥有子节点
	- element.children 返回节点元素集合
	- element.childNodes 返回子节点集合
	- element.parentNode 返回父节点
	- element.cloneNode(deep)克隆节点,包括所有属性及属性值(包括 onclick,但不包括 addEventListener,onclick=fn这种动态绑定)

---
**属性和样式操作**

	- element.getAttributeNode() 获取指定属性的节点
	- element.setAttribute() 为节点设置属性,若属性不存在多数情况下返回null,某些返回空字符串,所以判断是否有指定属性,应使用 hasAttribute
	- element.hasAttribute() 判断是否有指定属性
	- element.style 获取或者设置样式
	- element.className 获取或者设置属性
---
**创建,删除和替换节点**

	- document.createElement()  创建元素节点
	- document.createTextNode() 创建一个文本节点
	- document.createDocumentFragment() 创建DocumentFragment节点
	- element.appendChild() 向节点子节点列表的末尾添加新的子节点
	- element.removeChild() 删除节点;删除自己时,需要用 parentNode.removeChild()
	- document.replaceChild() 替换元素节点
	- document.replaceData() 替换文本节点() 不如直接操作nodeValue
---
**获取节点**

	- document.getElementById() 根据`id`返回一个 Element 对象,没有返回 null
	- document.getElementsByClassName
	- document.getElementsByTagName(name) 返回一个 HTMLCollection;在 WebKit 内核的浏览器中返回一个 NodeList
	- document.getElementsByName(name) name:是元素的 name 属性的值;返回一个 NodeList 集合
	- document.querySelector
		- 使用深度优先先序遍历文档的节点
		- 如果没有找到匹配元素,则返回 null
		- 返回第一个匹配的节点
	- document.querySellectorAll 返回一个 NodeList,并且是静态集合


### 高效遍历 DOM

- 使用`nodeType`过滤
- 使用XPath
- 遍历时尽量不对 DOM 操作

```
var allPara = document.getElementsByTagName('p');
for(var i = 0; i < allPara.length; i++) {
	allPara[i].appendChild(document.createTextNode(i));
}
→
var allPara = document.getElementsByTagName('p');
var collectTemp = [];
for(var i = 0; i < allPara.length; i++) {
	collectTemp[collectTemp.length] = allPara[i];
}
for(i = 0; i < collectTemp.length; i++) {
	collectTemp[i].appendChild(document.createTextNode(i));
}
collectTemp = null;
```

## Repaint and reflow

### 插入大量内容避免重绘和回流

- DocumentFragment 通过 js 操作 DOM树是很浪费性能的,尤其是增删会引起浏览器重新渲染.而`DocumentFragment`文档片段,并不存在于 DOM树中,而是存在于内存中,对其操作并不会引起重绘和回流.只需要将修改后的`DocumentFragment`插入到 DOM 中,这样只会引起一次浏览器渲染.需要注意的是:①往 DOM 树中插入文档片段,只会将其子节点插入 DOM 树中,文档片段也失去其子节点②将DOM树节点插入文档片段后,这些节点也会从 DOM 树消失

```
var docFragm = document.createDocumentFragment();
var elem, contents;
for(var i = 0; i < textlist.length; i++) {
	elem = document.createElement('p');
	contents = document.createTextNode(textlist[i]);
	elem.appendChild(contents);
	docFragm.appendChild(elem);
}
document.body.appendChild(docFragm);
```

- cloneNode

```
var original = document.getElementById('container');
var cloned = original.cloneNode(true);
cloned.setAttribute('width', '50%');
var elem, contents;
for(var i = 0; i < textlist.length; i++) {
	elem = document.createElement('p');
	contents = document.createTextNode(textlist[i]);
	elem.appendChild(contents);
	cloned.appendChild(elem);
}
original.parentNode.replaceChild(cloned, original);
```

- invisible

```
var posElem = document.getElementById('animation');
posElem.style.display = 'none';
posElem.appendChild(newNodes);
posElem.style.width = '10em';
// Other changes…
posElem.style.display = 'block';// block 块级元素
```

- Taking measurements
Taking measurements will force it to reflow
浏览器也会cache 操作,一次 reflow.但是进行测量操作时为了获取准确数据,会强制进行 reflow

### style 样式操作
- 在已知样式的情况下设置 class
```
div {
	background: #ddd;
	color: #000;
	border: 1px solid #000;
}
div.highlight {
	background: #333;
	color: #fff;
	border: 1px solid #00f;
}
…
document.getElementById('mainelement').className = 'highlight';
```
 - define a `new` style attribute for the element
兼容性:
- IE的 setAttribute 不支持设置'style'(另外设置"class"属性,IE为"className")
- 一些老浏览器,包括Opera 8,不支持 cssText
- 如果只修改一些style,可以拼接,但是IE6/7/8中 cssText 返回值少了分号

```
var posElem = document.getElementById('animation');
var newStyle = 'background: ' + newBack + ';' +
	'color: ' + newColor + ';' +
	'border: ' + newBorder + ';';
if(typeof(posElem.style.cssText) != 'undefined') {
	posElem.style.cssText = newStyle;
} else {
	posElem.setAttribute('style', newStyle);
}
```


## DOM事件

**event 对象**
- stopPropagation() 取消冒泡,IE 不支持
- cancelBubble = true 取消冒泡(IE)
- preventDefault() 取消默认行为 IE 不支持
- cancelable = false 表示没有默认行为 IE 不支持
- returnValue = false 取消默认行为(IE)
- javascript 的 return false 只会阻止默认行为,

- `jQuery`
	- return 则既阻止默认行为又防止冒泡
	- stopPropagation 取消冒泡,兼容IE
	- preventDefault  取消默认行为,兼容 IE
```
// 取消冒泡和默认行为
function cancelHandler(event){  
	if(window.event){
		window.event.cancelBubble = true;
		window.event.eturnValue = false;
	}else{
		event.stopPropagation();
		event.preventDefault();
	}
  return false;

	// jquery
	return false;  
}  
/*只取消冒泡*/
function stopHandler(event)  
    window.event?window.event.cancelBubble=true:event.stopPropagation();  

		// jquery
		event.stopPropagation();
}
/*只取消默认行为*/
function cancleDefault(event){
	return false;

	// with jquery
	event.preventDefault();
}
```
<!-- - target 属性 -->



<!-- - 浏览器引擎
	- Trident: 	IE
	- Gecko: 	Firefox
	- webkit:	Safari chrome ios -->




<!-- # BOM

## location

以 http://localhost:8081/dbmon/homeperf.html&tagsid=tagid3_tagid4 为例
window.location 对象,可不使用 window 这个前缀
	- location.protocol :"http:"
	- location.hostname :"localhost"
	- location.port 	:"8081"
	- location.host 	:"localhost:8081"
	- location.origin 	:"http://localhost:8081"
	- location.pathname :"/dbmon/homeperf.html"
	- location.search 	:"&tagsid=tagid3_tagid4"
	- location.href 	:"http://localhost:8081/dbmon/homeperf.html&tagsid=tagid3_tagid4"
	- location.assign() 方法,加载新的文档
	- location.reload(true/false) 方法 默认false从缓存中重载
	- location.replace() 不可以通过“后退”退回到原页面
导航到新的页面
	- location.assign("URL")
	- location = "URL"
	- location.href = "URL" 当前页面
	- location.reload(true) 强制重新加载当前页面
	- location.search = " " 向服务器发送字符串数据
    - location.hash 返回锚点值
获取 location.search 的一个键值
	- function loadPageVar (sVar) {
	  return decodeURI(window.location.search.replace(new RegExp("^(?:.*[&\\?]" + encodeURI(sVar).replace(/[\.\+\*]/g, "\\$&") + "(?:\\=([^&]*))?)?.*$", "i"), "$1"));
	  }
	- function GetParam(name) {  
		var reg = new RegExp("(^|&)"+ name +"=([^&]*)(&|$)");
		var r = window.location.search.substr(1).match(reg);
		if (r!=null) return unescape(r[2]); return null;
	  } -->
