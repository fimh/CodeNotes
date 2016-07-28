# HashMap

Android Version: 22  
Time: 2016/07/11  
Tag: Unsolved TODO: 6,7,8,15  

## 分析
前边先学习了Map的接口，为什么不直接看HashMap的实现呢？

打个不算太恰当的比喻，就像要做一件事情，我们先分析它的全局/整体，然后划分模块，最后根据不同的模块逐一去做。

先看接口还有另外一个好处，我们可以
* 学着设计
* 想想为何要这样设计
* 是不是目前的这些方法就够了
* 自己是不是可以设计出类似的方法体系
* 如果是自己，如何实现这些方法

了解了Map对外提供的方法之后，看HashMap的代码也会思路比较清晰。

我们先略过细节，可以发现HashMap的public方法就是Map中定义的那些(除了clone和构造方法)，而non-public的方法就是为了实现对外的接口而写的辅助方法。

---
Android版本的实现和JDK版本的确实不一样，不过核心思想与设计是一样的。

> 列出一点两者实现的不同：
> 因为HashMap允许存放一个key为null的entry，所以要有一个位置用来存放这个专门的entry，而其他的entry则根据hashcode来进行定位。  
>> Android版本使用了两个变量来存储entry：table:HashMapEntry<K, V>[], entryForNullKey:HashMapEntry<K, V>。很显然，null key的entry存放在后者。  
>> 
>> JDK版本则只使用了一个变量来存储entry：table:Entry<K, V>[]，在这里，null key的entry存放在table[0]的位置。

### 变量
#### 静态变量
首先，有两个私有常量定义了HashMap的容量，我们可以看到最小容量要求是2的2次方，最大容量是2的30次方。

接着有一个私有的静态表EMPTY_TABLE:Entry[]，它是被所有0容量的map所共用的，其实只是一个占位符，因为永远不会往里边写数据。它的初始容量之所以设置为2，是因为它在第一次resize时会创建一个最小容量的表，即4。

最后一个是DEFAULT_LOAD_FACTOR=.75F，文档中说本实现中是忽略掉load factor，但是因为API中有提及，所以代码中不能完全删掉。

#### 成员变量
成员变量分为2类，package可见于私有，但是它们都不参加序列化(都被transient所修饰)。

##### package可见
`table:HashMapEntry<K, V>[]`

> 存储HashMapEntry，但是如果key为null，则不存储在这个表中。

`entryForNullKey:HashMapEntry<K, V>`

> 根据变量名就可以看出来，此变量存储key为null的HashMapEntry。

`size:int`

> 存储了多少个，这个没什么好多说的。

`modCount:int`
> 用于检测同步修改，在看HashMap.HashIterator时会发现它的真正作用。

##### private
`threshold:int`
> 一个阈值，当size超过这个值时，table将被rehash。这个值一般是容量的0.75倍。

`keySet:Set<K>`
`entrySet:Set<Entry<K, V>>`
`values:Collection<V>`

> 上面3个成员变量都是延迟初始化。

### 方法
#### 构造方法
一共有4个：
* 前三个的参数主要是指定容量与load factor的不同组合(无参数其实是容量为0)。
* 最后一个是传入一个map，具体负责将传入的map拷贝到当前对象中则是调用成员方法constructorPutAll()。

对容量参数capacity的处理：
* 如果capacity<0，很简单，直接抛一个非法参数异常，该异常是RuntimeException，所以方法不用声明有异常抛出。
* 如果capacity==0，那么和无参的处理逻辑是一样的，注意threshold设置为-1，这样就会在put()方法首次调用时替换掉默认的EMPTY_TABLE。
* 如果capacity超过了上下限，那么重置边界。
* 如果capacity在上下限范围内，调用Collections.roundUpToPowerOfTwo(i:int)将其转化为2^n^，且>=i。

外部方法`Collections.roundUpToPowerOfTwo(i:int)`的处理：

> 这个算法的关键是开头的自减，和结尾的加1。这样保证了如果input==2^n^， 那么最后的结果不会变成2^n+1^ 。
* 如果前边的capacity>0，那么转向makeTable(int)方法。
* 如果指定了loadFactor，那么android版本的实现里边只是判断了loadFactor的合法性
	* 是否<=0
	* 是否为有效数字
* 如果不合法，那么抛出非法参数异常。
>
> 注意：android版本的实现会忽略loadFactor的值，而总是使用3/4(google的说法是为了简化代码和提高效率)。

#### 内部方法
`makeTable(newCapacity:int)`

> 该方法做的工作比较简单，根据newCapacity创建一个新的数组，然后计算threshold=0.75*newCapacity。
>
> 注意，计算threshold时并不是简单地*0.75，而是转变了一下: `threshold = (newCapacity >> 1) + (newCapacity >> 2)`。
>
> 这时我们也就明白了为什么google说使用3/4是为了提高效率，因为转化后的移位与相加操作比浮点数计算要快。

`constructorPutAll(map:Map)`

> 首先如果table还是EMPTY_TABLE，说明还是capacity=0的初始状态，那就就执行一次doubleCapacity()。
> 
> 然后对map.entrySet做一个foreach循环，然后调用constructorPut(key:K, value:V)进行具体的复制操作。
> 
> 注意，该方法虽然名字里边有constructor，但是并不是构造方法所独用的，像伪构造方法也可以使用(如clone，readObject)等。

`doubleCapacity()`

> 如果当前的capacity已经是MAXIMUM_CAPACITY，那么直接返回原来的table。
>
> 否则，以当前的capacity*2作为参数，调用makeTable。接下来
>
>> 如果size==0，说明table里边并没有存储数据，直接返回newTable即可。
>>
>> 否则，使用一个for循环遍历oldTable。
>>> 如果oldTable[j] != null，那么使用oldCapacity, oldTable[j].hash, j计算出一个值作为其在新表中的位置，放进去。
>>>
>>> 因为table[j]本身是一个单链表，然后遍历这个单链表。
>>> 对每一个元素计算其newHighBit
>>>> 如果与表头的highBit不相等，那么计算出一个在新表中的位置，放进去。
>>>>
>>>> 否则，放在broken的后边。

`constructorPut(key:K, value:V)`

> 这个方法的实现在put方法的基础上简化了一些步骤。
> 如在put之前并不会检查capacity是否够大(在constructor中确实没有必要)，也没有增加modCount变量的值。
>
> 注意，put里边最后调用的是addNewEntry方法，而这里调用的是constructorNewEntry。

`putValueForNullKey(value:V):V`

> 如果key为null，就会调用这个方法。
> HashMap使用另外一个成员变量`entryForNullKey`来存储key为null的entry。
>
> 它的逻辑和正常put类似，只不过简单很多。
> 因为HashMap只允许最多有一个entry的key为null，所以
>> 如果之前没有值，那么调用addNewEntryForNullKey。
>> 否则替换其value，返回旧值。

`addNewEntry(key:K, value:V, hash:int, index:int):void`

> 这个方法直接直接，使用传进来的参数创建一个新的HashMapEntry并且赋值给table[index]。

`addNewEntryForNullKey(value:V):void`

> 这个方法的写法与addNewEntry一样，只是创建新的HashMapEntry时的参数不同而已。
> 其中key=null, next=null, hash=0。

`constructorNewEntry(key:K, value:V, hash:int, first:HashMapEntry<K,V>):HashMapEntry<K, V>`

> 这个方法就是使用传进来的参数new一个`HashMapEntry<K, V>`。

`ensureCapacity(numMappings:int):void`

> 首先我们根据参数numMappings计算出一个新的capacity - `newCapacity`。
> 这里并不是直接把numMappings转化为2^n^，而是先调用capacityForInitSize(numMappings)，然后将其值作为参数再转化为2^n^。
>
> 然后，接下来的两个if判断也很有必要，即
>> 如果newCapacity<=oldCapacity，那么我们没有必要修改table，return即可；
>> 如果newCapacity==oldCapacity*2，那么我们调用doubleCapacity，然后return。
>
> 前面的if都没有满足，那么我们至少将oldTable扩大了4倍，此时我们根据newCapacity调用makeTable方法。
>
> 然后判断当前的map是否有值，即
>> 如果size!=0，那么接着走下边的两层循环。
>> 这里需要在纸上画一下执行过程，见TODO 11。

`removeNullKey():V`

> 这个方法很简单，因为entryForNullKey只存储一个key为null的entry。
> 
> 需要判断一下entryForNullKey==null，
>> 如果为true，表示entry为空，直接返回null即可。
>> 否则，拿到entryForNullKey.value(最后返回这个值)，然后将entryForNullKey置null，同时记得size减1，modCount加1。

`containsMapping(key:Object, value:Object):boolean`

> 我们前边阅读过containsKey方法之后，再来看这个方法就非常容易理解了。
>
> 它是在前者的基础上，即找到与key相等的entry之后，再判断一下entry.value与value是否相等：
>> 如果key与value都相等，那么才会返回true；
>> 否则false。

`removeMapping(key:Object, value:Object):boolean`

> 与containsMapping一样，removeMapping也可以找到其简单版本 - `remove`方法。
>
> 同样的，在remove代码的基础上，我们再加上entry.value与value相等的判断即可。

#### public方法
`put(key:K, value:V):V`

> 如果key==null，那么直接`return putValueForNullKey(value:V)`；
> 
> 否则，调用Collections.secondaryHash(key:Object)计算出一个hash值，然后再计算index值(见下方的TODO 9)。
>> 如果table[index]!=null，对其(单链表)进行遍历。
>>> 如果在table里边找到了与key相同的entry，将它的value修改为新值，然后返回旧值。
>
> 代码执行到这里，说明前边没有找到与key相同的entry，我们需要创建一个新的entry。
>> 先将`modCount++`(这个变量的作用见TODO 4)，然后将`size++`。
>>> 如果这时size>threshold，那么调用doubleCapacity增大容量，并且重新计算index。
>>
>> 然后调用addNewEntry将key/value加进去。

`get(key:Object):V`

> 如果key==null，那么直接取entryForNullKey这个entry，返回null或者entry.value；
>
> 如果不是，那么接着往下走。
> 和put里边一样，我们仍然使用Collections.secondaryHash方法计算出key的hash，再计算出index的值(hash&(table.length-1))。
>> 如果`table[index]!=null`，对其(单链表)进行遍历，直到找到与key相同的entry返回；
>> 否则返回null。

`containsKey(key:Object):boolean`

> 这个方法的实现和`get`基本上一模一样。
> 只是`get`找到与key匹配的entry，会返回entry.value；
> 而此方法会返回true。

`containsValue(value:Object):boolean`

> 这个方法的思路与containsKey完全不一样。
>
> 因为要查找的是value，所以无法像key那样计算hash快速定位到单链表。
> 只能老老实实循环，而且要两层循环，即算法复杂度是n^2^。
>
> 因为value为null或!null，判断相等的写法不一样，所以写了两遍双层循环进行遍历。
> 而且因为没有key的信息，所以entryForNullKey也要查询一下是否有符合条件的entry。
>> `value==null`，判断条件为`entry.value==null`
>> `value!=null`，判断条件为`value.equals(entry.value)`

`putAll(map: Map):void`

> 这个方法就两句：
> 第一句是调用`ensureCapacity`方法；
> 第二行是调用父类的方法`super.putAll`。
>
> 可以看出来，第一行是为了在真正putAll的时候有足够的空间来操作。

`remove(key:Object):V`

> 首先判断key，如果key==null，那么直接return调用removeNullKey()；
>
> 否则，接着往下走。
> 首先是获取key对应的index，取到index存储的entry单链表。
>> 获取key对应的index在HashMap的其它方法中出现过多次，如put, get, containsKey。
>
> 然后是遍历单链表：
>> 如果找到与key相同的entry，那么将其从单链表中删除；
>>> 从单链表中删除元素是有两种情况：
>>> 1. 要删除的元素是表头
>>> 2. 不是表头
>>
>>> 对它们的操作稍微不同
>
>> 否则return null。

`clear():void`

> Android版本的实现与JDK的实现稍稍不同。
> 前者比后者多了一个对于size的判断，如果size为0，就什么也不做。
>
> 这里借助`Arrays.fill`方法将table数组全部填null，有点类似于磁盘的格式化。

`keySet():Set<K>`

> 如果`keySet==null`，则新建一个KeySet对象返回；
> 否则，直接返回keySet对象。
>
> 理解KeySet，见TODO 12。

`entrySet():Set<Entry<K, V>>`

> 这个和`keySet`是一样的，只是返回的是EntrySet对象。
>
> 见TODO 12。

`values():Collection<V>`

> 也`keySet`, `entrySet`类似。
> 如果对应的成员变量为null，则返回一个新建的Values对象；
> 否则，直接返回对应的变量values。
>
> 见TODO 14。

## TODO
1. ~~最小容量为什么是4？容量为什么必须是2^n？~~

	> 最小容量为4是Android的实现，而JDK的实现是16。  
	> 容量必须是2^n则和根据key计算index有直接关系，即TODO 9。

2. ~~load factor的具体作用是什么？~~

	> load factor就是HashMap的负载因子，默认值是0.75(android版的实现中也是写死了0.75)，这是时间和空间成本上的一种折中。 
	>  
	> 增加负载因子可以减小Hash表(Entry数组)所占用的内存空间，但是会增加查询数据的时间开销，而查询是最频繁的操作(get()与put()方法都会用到查询)。  
	>
	> 减小负载因子会提高数据查询的性能，但是会增加Hash表所占用的内存空间，也会使得
	doubleCapacity()操作更加频繁。
    >
    > 参考：[java中HashMap详解](http://alex09.iteye.com/blog/539545/)

3. ~~HashMap既然实现了Serializable接口，为什么它自己的成员变量都是transient的？~~

	> 通过阅读writeObject方法和readObject方法可以得到答案。  
	>
	> 把成员变量都设置为transient并不是HashMap不序列化数据，而是要自己控制序列化的数据。
	> 
	> HashMap手动将table.length, size以int形式写入，而table数据则是通过foreach循环遍历entrySet，依次将entry.key, entry.value以Object的形式写入。
	> 
	> 而readObject时要做的工作就稍微多了一点，先读取capacity(即前边写入的table.length)，然后判断并将其转化成2^n。(这部分代码和构造方法中的一样)，然后调用makeTable(capacity)，最后根据size进行for循环，调用constructorPut方法依次写入entry。

4. ~~modCount的作用是什么？~~

	> Android版的实现中对临时这个成员变量的解释很简洁以致于搞不明白什么意思。我们可以参照JDK实现里的注释，初步了解它的作用，即记录内部mapping修改或者导致rehash的操作。
	> 
	> 这些方法都会执行modCount++的操作：put, putForNullKey, clear, remove, removeNullKey, removeMapping。
	> 
	> 这个变量的作用是使得HashMap的集合视图上的迭代器快速失败。
	> 
	> 在看过HashMap.HashIterator的源代码后理解了快速失败的含义。即  
	> 在迭代的过程中不允许HashMap的数据被修改，这个变量用于快速判断是否已经修改了。

5. ~~Float.isNaN的含义是什么，这个方法的必要性是什么？~~

	> 这个方法用于判断传入的数值是不是一些特殊值，如算术溢出、给负数开平方根、除以0等。
	> 
	> 参考：[Java中遭遇NaN](http://blog.csdn.net/shangzhenhui/article/details/6633562)

6. 【还是不太明白，需要再看一下】doubleCapacity方法中，highBit, nextHighBit, broken不明白。

	> entry在newTable中的index为什么这样计算，newIndex=j|(e.hash&oldCapacity)？

7. Object的hashCode()是怎么计算出来的？

8. Collections.secondaryHash(h:int)的算法待看

9. ~~put/get/contains/remove等方法中index=hash&(table.length-1)，为什么这么计算？~~

	> 这样计算是保证index的值在[0, table.length)之间，它在计算结果上等价于index=hash%table.length。
	>
	> 将取模操作换成&操作会提升计算速度(实际上能提升多少，我也没有验证对比过)。
	>
	> 这个转换的前提就是table.length必须是2^n，因为这个时候table.length-1换算成二进制就是n个1：
	> * 如果hash<=table.length-1，那么index=hash
	> * 如果hash>table.length-1，那么index=hash%length
	>
	大家可以自己在纸上演算一下。
	>
	> 参考：[Hashmap为什么容量是2的幂次，什么是负载因子 ](http://blog.csdn.net/a_long_/article/details/51594159)

10. ~~putAll方法先调用了ensureCapacity方法，然后再一个一个put。put方法本身已经有doubleCapacity机制，为什么还要先ensureCapacity？~~

	> 这样效率更高，第一步一次将capacity增加到足够大的空间，而不是后边的一次一次doubleCapacity。
	>
	> doubleCapacity内部的算法复杂度和ensureCapacity是一样的。

11. ~~在纸上画一画，理解ensureCapacity的执行执行过程。~~

	> 经过rehash之后，原来old table里的单链表的顺序会反转。
	>
	> 和put里的处理类似，当key经过二次hash计算得出index，而index发生碰撞时，会将新的entry插入单链表的表头。

12. ~~理解HashMap.KeySet/HashMap.EntrySet类，从而理解集合的迭代机制(即迭代器)。~~

	> 因为KeySet和EntrySet的原理是一样的(它们都继承自AbstractSet)，所以这里只分析KeySet。
	>
    > 看代码会发现，HashMap.KeySet本身并不包含数据，它只是对外提供一些专有的接口(对于key)，供外部方便调用。
    >
	> 而迭代器和KeySet类似，自身不包含数据，因为它和KeySet都是HashMap的内部类，所以直接访问HashMap的数据，对外按照Iterator的规范，提供了3个统一的接口用于遍历HashMap的数据，而使用者无需关心HashMap的数据时如何存储的。
	>
	> 这3个接口是：hasNext, next, remove，可以见知义。具体的代码实现，见HashMap.HashIterator的代码分析。

13. ~~分析下containsMapping, removeMapping。~~

	> 两者分别是containsKey, remove方法的稍微复杂的版本，即在前者的基础上同时加上对value的判断。

14. ~~分析HashMap.Values代码，从而了解Collection是什么？~~

	> 分析完HashMap.Values的代码会发现，它与HashMap.KeySet/EntrySet类似，都是最终实现了Collection接口。
	>
	> 因为都是HashMap的内部类，所以它们自身并没有存储数据，提供给外部的接口都是直接调用HashMap的内部数据来实现的。
	>
    > 而Collection是一个抽象的概念，可以理解为广义的数据集合。
    > 但是要注意，虽然Map也属于广义的数据集合，但是它是键值对，而Collection则是值的集合。
	>
    > 具体见Collection代码分析。

15. 理解Java序列化机制的工作原理(实现了Serialiable接口的类的成员变量是如何被自动序列化和反序列化的)

	> 阅读ObjectOutputStream/ObjectInputStream代码。