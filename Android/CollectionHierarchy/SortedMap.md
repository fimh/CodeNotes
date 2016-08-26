# SortedMap

Android Version: 22  
Time: 2016/07/26  
Tag: Finished  

## 分析
继承自`Map`接口，提供了对key排序的功能。
排序则按照key的自然排序或者指定的`comparator`进行排序。

这个接口一共新增了6个方法。

### 方法分组
新增的方法比较少，我们就不按照之前的分类方法进行分组了。

`comparator():Comparator<? super K>`

> 如果使用key的自然排序，返回null；
> 
> 否则，返回使用的comparator。

`firstKey():K`
`lastKey():K`

> 分别是返回Map中最小/最大的key。

`headMap(endKey:K):SortedMap<K, V>`
`tailMap(startKey:K):SortedMap<K, V>`
`subMap(startKey:K, endKey:K):SortedMap<K, V>`

> 这3个方法的作用类似，都是生成一个子Map，**只是子Map的起始范围不一样。**
> 
> 注意：`startKey`是包含自身的，而`endKey`则不包含自身。
> 并且在子Map/原始Map中更改同样会反应到另一个Map当中。
> 如何保证？见TODO 2

## ~~TODO~~
1. ~~为什么这个接口里边只有comparator的`get`方法，而没有`set`方法？~~

	> 大家看过`SortedMap`的实现类`TreeMap`后就明白了，因为**comparator在构造方法里边就指定好了，初始化之后是不允许再修改comparator的**。

2. ~~如何实现子Map/原始Map更改同样会反应到另一个Map当中？~~

	> 返回的子Map的实例对象的类是`TreeMap`的内部类，**其本身并不包含数据，数据完全使用TreeMap的**。
	> 
	> 与此相同的还有`HashMap.Values`, `HashMap.KeySet/EntrySet`。