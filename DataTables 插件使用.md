# DataTables 插件使用

标签（空格分隔）： DataTables JQuery

---


## 初步使用 ##
    DataTables 是 JQuery的一个表格插件,目前版本是1.10.13.

## 数据data ##
    俗话说,巧妇难为无米之炊. 作为表格插件的 DataTables 自然需要数据才能展现.
    本地数据可以直接在data 属性中指定.远程数据通过 ajax 加载.无论何种方式,数据格式必须是数组,可以是二维数组或者对象数组
    
    - 二维数组

    [
        [ "Tiger Nixon", "System Architect", "$3,120", "2011/04/25", "Edinburgh", 5421 ],
        [ "Garrett Winters", "Director", "$8,422", "2011/07/25", "Edinburgh", 8422 ],
        // ...
    ]

    - 对象数组

    [
        {
            "name":       "Tiger Nixon",
            "position":   "System Architect",
            "salary":     "$3,120",
            "start_date": "2011/04/25",
            "office":     "Edinburgh",
            "extn":       5421
        },
        {
            "name": "Garrett Winters",
            "position": "Director",
            "salary": "5300",
            "start_date": "2011/07/25",
            "office": "Edinburgh",
            "extn": "8422"
        },
        // ...
    ]
    "columns": [
        { "data": "name" },
        { "data": "position" },
        { "data": "office" },
        { "data": "extn" },
        { "data": "start_date" },
        { "data": "salary" }
    ]
    
    如果使用对象数组,必须在 columns 属性中指定对象名,用于数据解析,而二维数组不需要.但是推荐使用对象数组,对后续代码的增删改都有好处.
    
### ajax 
    当需要从后端加载数据时, ajax 是必须的, ajax 参数接受字符串|对象|函数.着重说明ajax 接受 对象为参数,基本继承了 jQuery.ajax 所有的选项.
    当以对象作为参数, 有几个重要的属性:
    - url 
    - data 发送给后端服务器的数据,比如{"startTime": 20161226}
    - dataSrc dataTables 额外添加的参数,用于解析数据 这种,告诉dataTables谁是"data",支持深度解析,如 json:data 一个优秀的 restFul 返回的数据格式中不止包括一个对象.如:
    {
        "data":[...],
        "url: ....,
        "statusCode" : ...,
        ...
    }

### 数据更新

### language

## 组件 

#### 组件搭配

#### 分页显示条数和翻页 

#### 搜索框  

#### info

## 排序 ##

## 保存状态 ##

## 加载慢 ##

## 自适应宽度 ##

默认 visiable:false(返回所有表格)
$.fn.dataTable.tables()
$.fn.dataTable.tables(false)
$.fn.dataTable.tables({visiable:false})
或者显示所有可见的表格
$.fn.dataTable.tables(true)
$.fn.dataTable.tables({visiable:true})
1.10.8版本
默认 visiable:false,api:false
$.fn.dataTable.tables({visiable:false,api:false})
返回所有可见表格的api
$.fn.dataTable.tables({visiable:true,api:true})

调整表格列宽度
```
var $dt = $.fn.dataTable.tables(true);
for(var i,j = $dt.length ; i < j ; i++){
	$('#'+$dt[i].id).DataTable().columns.adjust();
}
```
1.10.8版本
```
var $dtApi = $.fn.dataTable.tables({visiable:true,api:true]});
for(var i,j = $dtApi.length ; i < j ; i++){
	$dtApi.columns.adjust();
}
```







源码:
```
function ( visible )
	{
		return jQuery.map( DataTable.settings, function (o) {
			if ( !visible || (visible && $(o.nTable).is(':visible')) ) {
				return o.nTable;
			}
		} );
	}
```
1.10.8版及以上
```
DataTable.tables = DataTable.fnTables = function ( visible )
	{
		var api = false;
	
		if ( $.isPlainObject( visible ) ) {
			api = visible.api;
			visible = visible.visible;
		}
	
		var a = $.map( DataTable.settings, function (o) {
			if ( !visible || (visible && $(o.nTable).is(':visible')) ) {
				return o.nTable;
			}
		} );
	
		return api ?
			new _Api( a ) :
			a;
	};
```

## 子行 ##

## 服务器模式 ##



