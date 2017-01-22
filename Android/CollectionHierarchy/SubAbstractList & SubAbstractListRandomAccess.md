# SubAbstractList & SubAbstractListRandomAccess

Android Version: 22  
Time: 2017/01/22  
Tag: Finished  

## 分析
`SubAbstractList`和`SubAbstractListRandomAccess`类都是`AbstractList`的内部静态类。

### SubAbstractList
该类继承自`AbstractList`，重新实现了与数据操作相关的方法。
它持有原始数据对象的引用，所以说它自己也没有自己的数据。
与父类相比，它的方法像一种**代理**，加上了两种操作：
- 自己的`modCount`与`fullList.modCount`做比较，防止多线程环境下导致数据不一致(其实就是抛出一个`ConcurrentModificationException`异常)
- 判断index是否合法，因为该类只是**Sub**

### SubAbstractListRandomAccess
该类继承自`SubAbstractList`，并且实现了`RandomAccess`接口。

而`RandomAccess`接口本身并没有定义任何方法，它是一个marker interface。

## ~~TODO~~
1. ~~market interface是什么？~~

	> 是一个空接口，作为一种元数据来使用。可以参考下面文章：  
	> - [Marker interface pattern](https://en.wikipedia.org/wiki/Marker_interface_pattern)
	> - [What is Marker interfaces in Java and why required](http://javarevisited.blogspot.tw/2012/01/what-is-marker-interfaces-in-java-and.html)
	> [Marker Interfaces in Java](https://dzone.com/articles/marker-interfaces-java)
	> [Marker Interfaces in Java?](http://stackoverflow.com/questions/25850328/marker-interfaces-in-java)




