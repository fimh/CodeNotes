# AbstractSet

Android Version: 22  
Time: 2016/12/11  
Tag: Finished  

## 分析
AbstractSet是HashSet的抽象父类，该类里边其实只实现了1个方法 - `removeAll`(继承自Object类的方法除外)

### 方法
`removeAll(collection:Collection<?>):boolean`

> 有两个点需要注意一下：
> 1. 如果`collection`中有任何一个元素被删除，那么将会return true，而不是所有的都删除，才return true(之前我对这一点一致搞不大清楚)
> 2. 根据`size() <= collection.size()`的返回，有两种情况进行删除操作。
>> 其实就是哪一个元素少，遍历那个Collection。只不过删除对象时稍有不同
>> 这样设计的初衷是减少循环的速度，加快操作的执行效率
>> 实际删除的操作依赖于接口`Set.remove`和`Iterator.remove`

## ~~TODO~~




