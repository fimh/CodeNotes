# Queue

Android Version: 22  
Time: 2016/07/22  
Tag: Finished  

## 分析
Queue接口继承自Collection接口，这个接口自己定义了6个方法。

这6个方法分为3组：添加数据，取数据，删数据。
每组为2个：  
* 1个是抛异常的版本
* 1个是不抛异常的版本(返回null或false)。

### 方法分组
我们针对新增(部分继承)的方法分析一下。

* Create/Update:
	- add(e:E):boolean
	- offer(e:E):boolean

		> 两者的区别在于抛异常上。
		> 
		> 当Queue为容量限制的对象：
		>> 前者会抛出抛出异常(`IllegalStateException`)，表示由于容量限制的原因添加失败；
		>> 
		>> 后者则不抛异常，返回false表示添加失败。
		>
		> 在容量限制的情况下，推荐使用后者。
* Read:
	- 取数据:
		- element():E
		- peek():E

		> 这两个方法会返回队列的头部，但是不会将其删除。
		>
		> 区别在于，如果队列为空：
		>> 前者会抛异常(`NoSuchElementException`)；
		>> 
		>> 后者则只是返回null；
* Delete:
	- remove():E
	- poll():E

		> 这两个方法都是删除队列的头部，并且会将其返回。
		> 
		> 这两个方法的区别于上一组一样(`element`/`peek`)：
		> 
		>> 前者会抛异常；
		>> 
		>> 后者则是返回null。

## ~~TODO~~
