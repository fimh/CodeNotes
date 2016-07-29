# HashMap.HashIterator

Android Version: 22  
Time: 2016/07/21  
Tag: Finished  

## 分析
这个内部类很有意思，它有3个对外的接口：
`hasNext`, `nextEntry`, `remove`。

和Iterator接口定义的方法高度一致，但是并没有实现这个接口，而是由其子类来实现Iterator接口(next方法就是调用`nextEntry().value`)。

**为什么要这样设计，从这个内部类的定义往下看我们就能找到答案。**

> 在这个类下面有3个它的子类，分别是：
> `KeyIterator`, `ValueIterator`, `EntryIterator`。
>
> 它们除了`next()`返回的值不一样之外，其它的逻辑都是相同的。
> 所有才将它们共同的部分抽象成一个基类，避免了冗余代码的出现。

### 成员变量
共有4个，都是package visibile的：

`nextIndex:int`

`nextEntry:HashMapEntry<K, V>=entryForNullKey`

> 为什么初始值设置为entryForNullKey，见TODO 1

`lastEntryReturned:HashMapEntry<K, V>`

> 记录上一个返回的entry，remove方法里删除的就是这个

`expectedModCount:int=modCount`

> 初始化HashIterator时记录当时的modCount，在遍历时用于快速失败返回

### 方法
`HashIterator():void`

> 如果默认的`nextEntry==null`，那么继续执行。
>> if block内的逻辑是为了在table里边找到第一个`entry!=null`，在这里边我们即得到了有效的nextEntry，又计算出了它的index且赋值给nextIndex。

`hasNext():boolean`

> 很简单，`nextEntry!=null`。
>
> 可以看到，nextEntry指的确实是下一个元素。

`nextEntry():HashMapEntry<K, V>`

> modCount终于上场了。
>
> 前边我们看HashMap代码时了解到modCount的作用是**使迭代器快速失败**，当时并不明白这个快速失败究竟指的是什么，在这里我们可以找到答案。
>
> 第一行是一个判断，`if(modCount!=expectedModCount)`，那么就抛出一个`ConcurrentModificationException`异常。
> 意思就是如果在迭代时，HashMap中的数据元素被修改了(删除，增加，替换)，那么就会导致上面的异常抛出。
> 这种情况的发生一般是在多线程的环境下。
>
> 接下来还是一个判断，`if(nextEntry==null)`，那么抛出一个`NoSuchElementException`异常。
> 由此可以看到，我们在调用nextEntry之前，需要先调用hasNext方法，否则可能会导致异常的发生。
>
> 接下来的代码和构造方法里的if block的代码基本一致 - **找到下一个valid entry**。
> 不同的是，需要先用一个本地变量记录下来当前的nextEntry，因为这个方法最后要返回这个entry。

`remove():void`

> 这里，我们看到要删除的其实就是`lastEntryReturned`。所以，要先判断一下它是否为null：
>
>> 如果null，那么直接抛出一个`IllegalStateException`。
>>
>> 如果不是，接下来还是判断modCount是否修改了，这与nextEntry方法里边一样。
>> 然后调用`HashMap.remove(key:Object)`方法，删掉`lastEntryReturned`。
>> 最后记得要将`lastEntryReturned`置为null，并且更新`expectedModCount`的值(因为HashMap.remove方法会更新modCount的值)。


## ~~TODO~~
1. ~~为什么nextEntry的初始值设置为entryForNullKey？~~

	> 如果初始值为null，那么在构造函数里边无论如何都要查找有效的(!null)entry。
	>
	> 设置为`entryForNullKey`，那么在构造函数里边判断如果不是null，那么什么都不用做。
	>
	> 而如果在table数组里边选有效的entry，不借助循环代码块是无法找到的。
	> 并且从`entryForNullKey`开始判断也比较合理，因为后边的`nextEntry`方法里边就不用再考虑`entryForNullKey`的问题了。
	>
    > 所以初始设置为`entryForNullKey`最为合适。