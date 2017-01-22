# SimpleListIterator & FullListIterator

Android Version: 22  
Time: 2017/01/22  
Tag: Finished  

## 分析
这两个类均为`AbstractList`的内部类。
两者的区别在于前者是单向的(实现了`Iterator`接口)，而后者是双向的(实现了`ListIterator`接口)。

### SimpleListIterator
实现了`Iterator`接口的3个方法：

`hasNext():boolean`
`next():E`
`remove():void`

> 注意，所有的`Iterator`只是对外提供一套标准的接口用于变量数据集合，而数据还是原来集合里的数据。  
> 这个类内部使用`pos:int`来跟踪目前遍历到哪个位置了，而`lastPosition`则用于跟踪上一个的位置。一般情况下，两者的值是一样的。  
> `remove()`方法具体删除元素还是调用数据集合的`remove(int:position)`接口，而后边的position的设置决定了  
> **在没有再调用`next()`之前，`remove()`方法只能被调用一次。**

### FullListIterator
这个类继承自`SimpleListIterator`类，并且实现了`ListIterator`接口，所以它具有双向遍历和添加/修改元素的能力。

父类的成员变量`pos`和`lastPosition`已经完全可以实现向前遍历的接口。

而`add(object:E)`接口实际上是将`object`添加到了`pos+1`的位置上了。

关于`ListIterator`接口与`Iterator`接口的区别，详见前面的分析。

## ~~TODO~~




