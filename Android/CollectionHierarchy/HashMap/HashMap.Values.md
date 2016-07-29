# HashMap.Values

Android Version: 22  
Time: 2016/07/21  
Tag: Finished  

## 分析
这个类继承了`AbstractCollection`抽象类，最终实现了`Collection`接口。

观察它自身实现的方法，可以发现其与`EntrySet`, `KeySet`的方法很像。

> 因为他们都是从`Collection`这个基本的接口派生下来的。

### 方法
它自身实现了5个方法(其实我们在阅读`HashMap.KeySet`/`HashMap.EntrySet`时已经见过了，这里只是列一下)：

`iterator():Iterator<V>`

> 最终返回的是`ValueIterator`对象，关于这个类可以见`HashMap.HashIterator`的代码分析。

`size():int`

`isEmpty():boolean`

`contains(o:Object):boolean`

`clear():void`

## ~~TODO~~
