# HashMap.HashMapEntry

Android Version: 22  
Time: 2016/07/22  
Tag: Finished  

## 分析
这个类与`HashMap`的其它几个内部类不同：这个类是静态的，实现了`Map.Entry`接口。

`Map.Entry`接口我们前面分析过，对外提供的接口很简单：
`getValue`, `setValue`, `getKey`。

这个类的关键点是它有一个`next:HashMapEntry<K, V>`成员变量，这样就形成了一个单链表。

它的构造函数里边有一个`next:HashMapEntry<K, V>`的参数，这样新建一个`HashMapEntry`对象就自动把自己插入到了`next`指向的单链表的表头了。

这样的设计，使得`HashMap`插入新entry的时候就非常方便：
只需要新建一个`HashMapEntry`对象，然后将其赋值给`table[index]`即可。

## ~~TODO~~
