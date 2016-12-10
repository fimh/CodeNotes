# HashSet

Android Version: 22  
Time: 2016/12/10  
Tag: Unsolved TODO: 1  

## 分析
HashSet实现了Set接口(其实是Collection接口，因为Set接口没有增加任何方法)，我们可以对照着Collection接口的分析来进行阅读HashSet类。

### 成员变量
`transient HashMap<E, HashSet<E>> backingMap`

> HashSet的数据全部是存放到这个实例变量中，也就是说HashSet其实是HashMap的一个Wrapper。

### 方法
#### 构造方法
一共有4个：
* 前三个都依赖于第四个构造方法
* 第三个构造方法(参数为`collection:Collection<? extends E>`)里可以看出来：
	* 如果collection的size < 6，那么新的size为11，否则为size的2倍（至于为什么选6，这里就不清楚了）
	* 通过foreach循环调用`add`将元素逐个加进去
	* 其实在这里也可以调用`addAll`方法将`collection`添加进去，效果和上面的foreach循环一样

#### public方法
`add(object:E):boolean`
`clear():void`
`contains(object:Object):boolean`
`isEmpty():boolean`
`remove(object:Object):boolean`
`size():int`

> 这些方法是直接调用`backingMap`的同名方法
> **所有`key`对应的`value`均为HashSet对象本身，其实也就是利用了`HashMap`的`key`不重复的特性来实现了`Set`接口。**

`iterator():Iterator<E>`

> 则是调用`backingMap.keySet()`的同名方法

`removeAll(collection:Collection<?>):boolean`

> 参考父类`AbstractSet.removeAll()`

`addAll(collection:Collection<?>):boolean`
`containsAll(collection:Collection<?>):boolean`
`retainAll(collection:Collection<?>):boolean`

> 这三个批量处理方法依赖于对应的单个处理方法，实现在父类`AbstractCollection`里

`toArray():Object[]`
`toArray(contents:T[]):T[]`

> 实现在父类`AbstractCollection`里

## TODO
1. 既然HashSet实现了Serializable接口，那么为什么保存数据的唯一变量使用transient来进行修饰，这样不就造成序列化并不会保存任何已存储的数据吗？



