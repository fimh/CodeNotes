# List

Android Version: 22  
Time: 2016/12/11  
Tag: Finished  

## 分析
`List`是一个很重要的数据结构，与`Set`一样，它也是直接继承自`Collection`接口。

前面我们分析过`Set`接口，可以拿它与`List`做一个对比：  
* List有了顺序的概念
* 在`Collection`之外，List自己定义了一些接口

如果你仔细观察的话，会发现所有新增的接口都与**顺序**关系。

### 方法
* Create/Update:
	- add(location:int, object:E):boolean
	- addAll(location:int, collection:Collection<? extends E>):boolean
	- set(location:int, object:E):E
* Read:
	- 取数据:
		- listIterator():ListIterator<E>
		- listIterator(location:int):ListIterator<E>
		- get(location:int):E
		- sublist(start:int, end:int):List<E>
			- **注意：返回的List与原始的List共享同一个数据源** 
	- 查询:
		- indexOf(object:Object):int
		- lastIndexOf(object:Object):int
* Delete:
	- remove(location:int):E

## ~~TODO~~

