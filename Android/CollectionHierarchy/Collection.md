# Collection

Android Version: 22  
Time: 2016/07/09  
Tag: TODO 3  

## 分析
Collection接口是集合框架中的根 - 它定义了数据集合的操作和行为。

任何直接或者间接实现Collection接口的类至少要有两个构造函数： 

`constructor()`

> 创建空集合

`constructor(collection:Collection)`

> 用于转换集合类型


并非所有实现Collection接口的类都要强制实现这两个构造函数，但是在
java.util包下面的类必须要这么做。

如果实现类对某个方法选择不予支持，那么可以抛出`UnsupportedOperationException`异常。

这个接口继承了接口`Iterator<T>`，该接口只有1个方法:
`iterator():Iterator<T>`
是不是很熟悉？
前面分析HashMap.KeySet/EntrySet, HashMap.Values()都有这个方法，因为它们都间接地实现了Collection接口。

### 方法分组
* Create/Update:
	- add(object:E):boolean
	- addAll(collection:Collection<? extends E>):boolean
* Read:
	- 取数据:
		- iterator():Iterator<E>
		- toArray():Object[]
		- toArray(array:T[]):T[] - 见TODO 2
	- 查询:
		- contains(object:Object):boolean
		- containsAll(collection:Collection<?>):boolean
		- isEmpty():boolean
		- size():int
* Delete:
	- remove(object:Object):boolean
	- removeAll(collection:Collection<?>):boolean
	- retainAll(collection:Collection<?>):boolean - 见TODO 1
	- clear():void
* 其它(从Object继承的2个方法)：
	- equals(object:Object):boolean
	- hashCode():int

## TODO
1. ~~理解retainAll(collection:Collection<?>)的含义。~~

	> retain是保留的含义，这个方法是保留与collection参数里相同的元素，其它的都删除掉。
	>
    > 所以它本质上是一个删除类别的方法。

2. ~~注意toArray(array:T[]):T[]的用法。既然有了toArray():Object[]，为什么还要有这个函数？~~

	> toArray():Object[]比较直观，将Collection对象转换成数组。但是有个问题 - 类型丢失。
	> 在没有泛型的时代(Java 1.5之前)，这不是个问题。
	> 但是有了泛型之后，我们不能想当然的把返回值Object[]强制转型为其原来的类型，这样会抛出ClassCastException。
	>
	> 所以为了保留类型信息，引入了toArray():Object[]的泛型版本。
	> 
	> 单看Collection里的文档描述也看不明白，我们可以看一个实现的代码就明白了。
	> 可以看ArrayList里的实现方法。具体分析这里就不贴了。
	> 
	> 看过代码，我们发现参数array:T[]其实只是一个容器，它有两个作用：
	> * 提供容器(返回时还是这个对象)
    > * 提供类型信息

3. 集合框架内都有哪些数据类型，它们之间的关系是什么？

	> 数据类型有：
	>> 接口：Set, List, Map, Queue, Deque, Enumeration, SortedMap, SortedSet  
	>> 实现Set的类：EnumSet, HashSet, LinkedHashSet, TreeSet  
	>> 实现List的类：ArrayList, LinkedList, Vector, Stack  
	>> 实现Map的类：HashMap, LinkedHash, ArrayMap, EnumMap, Hashtable, TreeMap, WeakHashMap  
	>> 实现Queue的类：ArrayDeque, LinkedList, PriorityQueue  
	>> 实现Deque的类：ArrayDeque, LinkedList  
	>> 实现Enumeration：StringTokenizer  
	>> 实现SortedMap的类：TreeMap  
	>> 实现SortedSet的类：TreeSet  
