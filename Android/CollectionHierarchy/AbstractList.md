# AbstractList

Android Version: 22  
Time: 2017/01/07  
Tag: Finished  

## 分析
AbstractList是`LinkedList`和`ArrayList`的抽象父类，它实现了`List`接口。(但是有些对单个元素操作的方法是留给实体子类来进行实现的)。

### 方法
#### 留给子类实现的方法
`get(location:int):E`

> 抽象方法，留给子类实现

`add(location:int, object:E):void`
`remove(location:int):E`
`set(location:int, object:E):E`

> 这3个方法没有具体实现，都只是一句`throw new UnsupportedOperationException();`。  
> 作用和抽象方法类似，但是不同的是 - 子类可以选择是否支持这项**Operation**。

#### 依赖于`ListIterator`的方法
`addAll(location:int, collection:Collection<? extends E>):boolean`

> 思路：使用参数`collection.iterator()`进行遍历，遍历的时候调用对单个元素操作的方法`add(location:int, object:E):void`将元素逐个添加进去。

`removeRange(start:int, end:int):void` - protected方法

> 先调用`listIterator(location:int):ListIterator<E>`方法获取`iterator`，然后在[start, end)之间进行遍历并且删除操作。  
> **注意：使用`Iterator`进行遍历时可以删除元素，而使用foreach循环时，则不能删除元素。**

`indexOf(object:Object):int`
`lastIndexOf(object:Object):int`

> 这两个方法正好完全对应，一个是找到第一次出现`object`的index，而另一个则是最后一次出现的index。  
> 所以两者的实现代码在形式上是一模一样，区别在于对`listIterator`遍历的方向：前者是沿着`next`的方向，而后者则是沿着`previous`的方向。  
> 我们在前边阅读过`ListIterator`的代码，这时候也可以理解它存在的意义了 - 方便我们进行双向遍历。  
> **注意：由于List当中可以添加null的元素，所以对参数`object`也要考虑null与!null 2种情况。**

`iterator():Iterator<E>`

> 新建一个实现了`Iterator`接口的内部类`SimpleListIterator`的实例，然后将其返回。

`listIterator():ListIterator<E>`
`listIterator(location:int):ListIterator<E>`

> 第一个方法直接调用第二个方法。  
> 第二个方法新建一个实现了`ListIterator`接口的内部类`FullListIterator`的实例，然后将其返回。

#### 其它方法
`add(object:E)boolean`

> 将`object`添加到List的末尾，调用了`add(location:int, object:E):void`方法

`subList(start:int, end:int):List<E>`

> 代码有11行，其实逻辑非常简单 - 首先判断输入参数的合法性，然后根据自身是否实现了`RandomAccess`接口来返回不同的`List`实例。  
> 实现实例的2种类都是`AbstractList`的内部类，分别为：  
> * `SubAbatractList`
> * `SubAbstractListRandomAccess`  
> **注意：Sub List与原始的List共享同一个数据源。**

`clear():void`

> 调用protected方法 - `removeRange(start:int, end:int):void`

## ~~TODO~~




