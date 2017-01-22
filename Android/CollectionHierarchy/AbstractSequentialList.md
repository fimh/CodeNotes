# AbstractSequentialList

Android Version: 22  
Time: 2017/01/22  
Tag: Finished  

## 分析
该类继承自`AbstractList`，但是本身依然是一个抽象类。

### 方法
该类实现了下面的几个方法：  

`add(location:int, object:E):void`
`addAll(location:int, collection:Collection<? extends E>):boolean`
`remove(location:int):E`
`set(location:int, object:E):E`
`get(location:int):E`
`iterator():Iterator<E>`

这些方法的实现看起来是一种**间接**操作，对数据的操作都是通过`ListIterator`来间接实现的。  

虽然实际的实现中并不会这样写，但是它给了我们一些思路：
- 站在一个更高的抽象角度来尝试解决问题
- 充分利用现有资源来解决问题

## ~~TODO~~




