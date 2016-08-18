# Deque

Android Version: 22  
Time: 2016/07/26  
Tag: Finished  

## 分析
`Deque`接口继承自`Queue`接口，与`Queue`接口相比，这个接口自己定义的方法要多很多，有17个。

但是我们稍加观察就会发现，这些方法具有很强的规律性，所以理解起来也很容易。

这个接口的特点是**针对Queue原有的方法都拓展了一组xxxFirst/xxxLast方法**。

### 方法分组
我们针对新增(部分继承)的方法分析一下。

* Create/Update:
	- addFirst(e:E):void
	- addLast(e:E):void
	- offerFirst(e:E):boolean
	- offerLast(e:E):boolean

		> 这两组方法理解起来很容易，`addFirst`/`offerFirst`是将e插入到队列的头部。
		>
		> 而`addLast`/`offerLast`则与add/offer等价，即将e插入到队列的尾部。
		> 
		> add与offer的区别我们在Queue里边也分析过了，容量限制的情况下:
		> **前者抛异常，后者返回false**。
		> 
		> 这里有一个问题，见TODO 1
* Read:
	- 取数据:
		- getFirst():E
		- getLast():E
		- peekFirst():E
		- peekLast():E

		> 这两组方法的区别和父接口`Queue`里的`element`/`peek`的区别一样：
		> **如果获取的元素不存在，前者抛出NoSuchElementException异常，后者返回null。**
		> 
		> `Queue`里的`element`方法到这里改名成了`getFirst`/`getLast`，见TODO 2
* Delete:
	- removeFirst():E
	- removeLast():E
	- pollFirst():E
	- pollLast():E

		> 同样，**如果队列为空，前者抛异常，后者返回null**。

	- removeFirstOccurrence(o:Object):boolean
	- removeLastOccurrence(o:Object):boolean

		> 这两个方法是"完全新增"的方法。
* Stack(根据语义，单分一组更合理):
	- push(e:E):void
	- pop():E

		> 这两个方法是针对Stack这种数据结构定义的接口方法。
		> 它们都是针对队列头部进行操作。
		> 
		> 分别等价于addFirst、removeFirst方法。
		> 所以它们在操作失败时也同样会抛出异常。

## ~~TODO~~
1. ~~为什么addFirst/addLast的返回类型是void，而add的返回类型是boolean？~~

	> 历史遗留问题，add返回类型为boolean，是由Collection接口定义好的。
	> 
	> 而根据`Queue.add`的定义，添加失败时，add是通过抛出异常来提示调用者，而不是返回的boolean值。
	> 
	> 而`addFirst`/`addLast`是新增的方法，就把这个没有使用的boolean改为void了。

2. ~~Queue里的element方法为什么改名为getFirst/getLast了？~~

	> 因为Queue是单向队列，添加元素都是到队尾，删除元素/取元素都是队首。element还比较合适。
	> 
	> 而Deque是双向队列，叫`elementFirst`/`elementLast`没有`getFirst`/`getLast`直观。