# Map

Android Version: 22  
Time: 2016/10/17  
Tag: Finished  

## 分析
前面分析Map的时候，提到在Objective-C中，它叫做Dictionary。其实Java中也有Dictionary，这是一个现在已经过时的抽象类。

它在JDK 1.0就已经存在了，可能早期考虑的不够完善，该类被定义成了一个抽象类。但是你查看了源代码会发现，它只是声明了抽象方法，并没有一点实现。所以更应该定义成接口。

我们对比前边的Map来看，该类定义的方法就简单很多 - 只有7个方法(构造方法除外)。

老习惯，我们依然可以按照CRUD的思想进行归类。

### 方法分组
* Create/Update:
	- put(key:K, value:V):V
* Read:
	- 取数据:
		- keys():Enumeration<K>
		- get(key:Object):V
		- elements():Enumeration<V>
	- 查询:
		- isEmpty():boolean
		- size():int
* Delete:
	- remove(key:Object):V

与Map接口相比，我们可以发现：
* Dictionary只有对单元素的操作
* Dictionary对外提供的接口里边，没有Map.Entry的概念

## ~~TODO~~
1. ~~这个类已经过时的，为什么还分析？~~

	> 其实通过分析它，我们可以了解到Map接口是怎么来的。  
	> 从接口的层面来看，Map是Dictionary融入了批量操作与key-value pair(即Map.Entry)的改良版本。
	> 
	> 从另外一个层面也可以看到，程序是多版本迭代逐渐成熟的，所以第一个版本不要想着做的/设计的太完美而误了发布的时间。
