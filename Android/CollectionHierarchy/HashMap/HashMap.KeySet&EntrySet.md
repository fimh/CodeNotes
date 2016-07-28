# HashMap.KeySet & HashMap.EntrySet

Android Version: 22  
Time: 2016/07/21  
Tag: Finished  

## 分析
HashMap.KeySet和HashMap.EntrySet这两个类是sibling关系。
它们都依次继承了AbstractSet,AbstractCollection抽象类，

这些抽象类我们先不用研究，先来看看KeySet/EntrySet自身的逻辑。

### HashMap.KeySet
这个类的实现很简单，一共也就22行代码。

由于是HashMap的内部类，所以可以访问它的成员变量和方法。

它可以看做是一个代理，并没有自己成员变量和逻辑处理方法。

除了newKeyIterator外，都是访问HashMap的变量或者方法。

#### 方法
`iterator():Iterator<K>`

> 直接返回一个新建的HashMap.KeyIterator对象。
> 见TODO 1
> 
> 这个类自己只实现了一个方法next，其它的都是从父类HashMap.HashIterator继承的。
>> next():K
>>> 实现很简单，返回nextEntry().key。

`size():int`

`isEmpty():boolean`

`contains(Object o):boolean`

> 调用HashMap.containsKey(key:Object)

`remove(Object o):boolean`

> 调用HashMap.remove(key:Object)

`clear():void`

> 调用HashMap.clear()

### HashMap.EntrySet
前边分析了KeySet类，这个类就简单很多。

它的接口与KeySet是一样的，其中size, isEmpty, clear三个方法的实现与KeySet是一样的。

#### 与HashMap.HashSet实现不一样的方法
`iterator():Iterator<Entry<K, V>>`

> 与KeySet.iterator()一样，只不过这里返回的是一个HashMap.EntryIterator对象。
>
> 而EntryIterator的实现见HashIterator代码分析。

`contains(o:Object):boolean`

> 首先判断参数的类型是否为Entry，如果不是，直接return false。
>
> 如果是，那么调用HashMap.containsMapping(key:K, value:V)

`remove(o:Object):boolean`

> contains方法类似，不过最后调用的是HashMap.removeMapping(key:K, value:V)。

## ~~TODO~~
1. ~~研究HashMap.HashIterator的实现，了解迭代器是怎么工作的(并且也能知道HashMap.modCount的实际作用)~~

	> 具体见HashMap.HashIterator的代码分析。
	>
	> HashMap.modCount的作用见HashMap的代码分析里的TODO。