# SortedSet

Android Version: 22  
Time: 2016/07/26  
Tag: Finished  

## 分析
前边分析了`SortedMap`的源码，再看`SortSet`简直易如反掌。
这个接口的设计与`SortedMap`一模一样(方法名的后缀除外)。

继承自`Set`接口，提供了对元素排序的功能。
排序则按照key的自然排序或者指定的`comparator`进行排序。
与`SortedMap`一样，这个接口一共新增了6个方法。

### 方法分组
和`SortedMap`一样(新增的方法比较少)，我们就不按照之前的分类方法进行分组了。

`comparator():Comparator<? super K>`

> 如果使用key的自然排序，返回null；
> 
> 否则，返回使用的comparator。

`first():K`
`last():K`

> 分别是返回Set中最小/最大的元素。

`headSet(end:K):SortedMap<K, V>`
`tailSet(start:K):SortedMap<K, V>`
`subSet(start:K, end:K):SortedMap<K, V>`

> 这3个方法的作用类似，都是生成一个子Set，**只是子Set的起始范围不一样。**
> 
> 注意：`start`是包含自身的，而`end`则不包含自身。
> 并且在子Set/原始Set中更改同样会反应到另一个Set当中。
> 如何保证？原理见`SortedMap`代码分析的TODO 2

## ~~TODO~~
1. ~~既然SortedSet有了顺序，那么能否与List相互替代呢？~~

	> 完全不能。
	> 首先，`SortedSet`与`SortedMap`一样，它的顺序是根据`comparator`计算得来的。
	> 而`List`的顺序是指元素插入的先后顺序。
	> 
	> 其次，`SortedSet`本质还是`Set`，不允许有元素重复。
	> 而`List`则没有这个限制。
