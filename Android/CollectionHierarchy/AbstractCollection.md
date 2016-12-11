# AbstractCollection

Android Version: 22  
Time: 2016/12/11  
Tag: Finished  

## 分析
从AbstractCollection这个名字可以看出来/猜出来：  
* 这个类是一个抽象类
* 这个类实现了`Collection`接口

由于这个类比较generic/abstract，所以它的一些方法的实现都是基于现有接口来进行实现的，你会看到大部分方法的实现有2个特点：  
* 循环遍历
* 依赖于iterator

由于它比较抽象，所以在具体的数据结构中(子类)，这些实现很有可能会被子类所重写，以根据各自的数据结构特点编写高效的处理逻辑。由此，我们也可以联想到经常出现的一种情况：  
> 一般比较通用的代码在处理特定逻辑时效率不是最高的，而效率最高的代码往往通用性并不高。

所以我们时常要根据需要在这两者之间做一个平衡。

### 方法
#### 批量处理的方法
`addAll(collection:Collection<?>):boolean`
`containsAll(collection:Collection<?>):boolean`

> 这两个方法都是遍历参数`collection`的`iterator`。  
> `addAll`是依次将`collection`中的元素添加进来。  
> `containsAll`需要注意的是如果`collection`中的元素有任何一个不在当前的类里边，那么就停止循环，返回false。

`removeAll(collection:Collection<?>):boolean`
`retainAll(collection:Collection<?>):boolean`

> 这两个方法的思想是一模一样的，都是遍历当前对象的`iterator`，然后逐一判断是否符合条件，执行删除操作。  
> 两个方法在实现上就差一个字符`!`，就在判断时正好相反，前者是当前元素在`collection`中符合条件，后者正好相反。

#### 单个处理的方法
`clear():void`

> 仍旧是遍历`self.iterator`，然后逐一进行删除。  
> 这个方法把`Iterator`接口的三个方法都用上了。在不知道具体的数据结构之前，这种方式应该是最好，最直观的方式了。

`isEmpty():boolean`

> 依赖于`size()`方法。

`remove(object:Object):boolean`

> 该方法的实现也是遍历`self.iterator`，如果object与当前的元素相等，则删除。  
> 由于判断null与非null的相等写法不一样，所以有了两种情况下的循环遍历。

#### toArray
`toArray():Object[]`
`toArray(contents:T[]):T[]`

> 这两个方法的实现都依赖与一个内部方法`toArrayList`，见下面的分析。

`toArrayList():ArrayList<Object>`

> 实现思想很简单  
> * 构建一个新的`result:ArrayList`  
> * 然后循环当前对象，将元素逐个加到`result`当中  
> * 最后返回`result`

## ~~TODO~~
