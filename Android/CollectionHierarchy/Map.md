# Map

Android Version: 22  
Time: 2016/07/09  
Tag: Finished  

## 分析
Map<K, V>接口是集合框架中最常见的一种数据结构，在各种语言中都会用到。
在Objective-C中名字不同，叫做Dictionary，其它语言中一般还是叫做map/Map。

在Map接口中有一个内嵌的Entry<K, V>接口，它封装了键值对。

Map自身定义了14个方法，因为它是一个数据容器，我们可以按照CRUD的思想将方法归类。

因为添加与修改数据在Map里边是混在一起的，所以合在一起。
而equals与hashCode方法是继承自Object的方法，这里就不再列出了。

### 方法分组
* Create/Update:
	- put(key:K, value:V):V
	- putAll(map:Map):void
* Read:
	- 取数据:
		- entrySet():Set<Map.Entry>
		- keySet():Set<K>
		- get(key:Object):V
		- values():Collection<V>
	- 查询:
		- containsKey(key:Object):boolean
		- containsValue(key:Object):boolean
		- isEmpty():boolean
		- size():int
* Delete:
	- remove(key:Object):V
	- clear():void

根据写和删除数据的方法可以发现如下规律：
* 一个方法对单个元素操作
* 一个方法对批量元素操作

而读的方法就比较多，共8个。
我们可以再分，取数据和查询。
取数据可以取到key、value和entry。
而查询则可以查询是否包含key或value，是否为空以及Map的size。

### Map.Entry<K, V>
这个接口比较简单，除了从Object继承的equals和hashCode外，自身的只有3个方法：
* getValue():V
* getKey():K
* setValue(value:V):V

注意：Map和Map.Entry接口里，写数据的方法返回的V指的是被覆盖的原来的value，
所以也有可能是null。

## ~~TODO~~
1. ~~values()方法返回的是另外一个接口Collection，我们以后再详细研究。~~

	> Collection接口是集合框架的根，定义了数据集合的最基本的操作。  
	> 具体见Collection代码分析。
