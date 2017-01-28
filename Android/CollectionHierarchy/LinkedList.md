# LinkedList

Android Version: 22  
Time: 2017/01/25  
Tag: Finished  

## 分析
前面分析了那么多的接口与抽象类，这次终于到了一个"正常"的类了 - `LinkedList`。

`LinkedList`是被使用频率非常高的一个对象容器，它除了实现了`List`接口外，还实现了  
* `Deque`
* `Queue`
* 2个marker interface - `Cloneable`, `Serializable`

所以这个类的方法会非常多。与抽象基类最大的区别在于  
* **它的数据操作方法的实现是根据自己数据结构的特点进行特定设计的，而不是间接的操作数据**

### 内部静态类 `LinkedList.Link<ET>`
这个类是`LinkedList`的数据单元，有3个成员变量  
* 数据对象 `data:ET`
* 双向引用 `previous, next:Link<ET>`

正是通过这两个引用，我们知道我们上个元素、下个元素是谁

### 内部静态类 `LinkedList.LinkIterator<ET>`
实现了`ListIterator`接口。

不知道大家是否还记得`AbstractList$FullListIterator`的实现，两者的思想其实是相似的，但是具体实现是不同的。

与`AbstractList$FullListIterator`不同的是，该类对数据操作与遍历时并没有使用数据源的数据接口，而是自己直接对数据进行操作。

#### 成员变量
`pos, expectedModCount:int`  

> 由于`LinkedList`的数据元素是前后以此相连的，其中的每一个元素并没有一个`position`的概念，所以需要单独的变量`pos`来记录当前遍历到的位置
>   
> 而`expectedModCount`则与之前一样，防止多线程环境下数据产生不一致的情况

`list:LinkedList<ET>`  
`link, lastLink:Link<ET>`  

> 由于这个类是静态类，所以持有了原始数据源的引用`list`
>  
> 而`link, lastLink`则与`AbstractList$FullListIterator`里的`pos, lastPosition`的作用类似 - 可以直接取到上一个与下一个元素

#### 方法
`LinkIterator(object:LinkedList<ET>, location:int)`

> 这个构造方法里边需要注意一点：根据`location`找到对应的元素。这里采用了**一分为二**的思想，即
> * `location < size / 2`，从头往后找
> * 否则，从尾部向头找
> 
> 这样保证了最大查找次数为 `size / 2`

`add(object:ET):void`  
`remove():void`

> 添加删除就比较简单了(虽然代码也有很多行)，**本质就是操作引用**

其它方法就更为直观了，移动`link`, `lastLink`的引用

### 内部类`LinkedList$ReverseLinkIterator<ET>`
**这个内部类其实应该定义为static**，因为它本身已经持有原始数据源的引用`list:LinkedList<ET>`。

该类只实现了`Iterator`接口，从名字就可以看出来，它是反向遍历List的。

### `LinkedList` 
#### 成员变量
该类自己声明的成员变量只有2个：  
`size:int - transient`  
`voidLink:Link<E> - transient`  

> `voidLink`是非常重要的一个成员变量，虽然自身的`data`为空。它连接了整个`LinkedList`的首位两端。

而从父类`AbstractList`继承过来的有一个成员变量：  
`modCount:int - transient`

这3个成员变量都是用`transient`进行修饰，那么在序列化与反序列化时对应的成员变量会被忽略。需要自己实现逻辑进行处理。

#### 序列化与反序列化
该类实现了`Serializable`接口，并且实现了与之相对应的2个方法：  
`writeObject(stream:ObjectOutputStream):void`  
`readObject(stream:ObjectInputStream):void`  

> 由于所有的成员变量都被`transient`关键字修饰，那么`ObjectOutputStream`/`ObjectInputStream`并不会对这些成员变量进行处理，就需要根据自己的需求在上面的这两个方法里边自行处理。
> 
> 序列化的逻辑很简单，首先调用默认的序列化方法 - `ObjectOutputStream.defaultWriteObject`，然后将`size`写进去，最后使用`Iterator`进行遍历，将每个元素写进去。
> 
> 反序列化的逻辑与之相反，恢复元素的时候稍微繁琐一些，因为要还原元素之间的链接关系，即 - `Link`的`previous`与`next`的赋值。

#### 方法
##### 添加元素
`add(location:int, object:E):void`

> 由于指定了插入的`location`，所以需要先找出来对应的元素`link`。这里采用的方法就是前边`LinkIterator(object:LinkedList<ET>, location:int)`里边所使用的方法 - ***一分为二***查找。
> 
> 接下来的工作就比较简单了  
> 新建一个`newLink:Link`，将前后设置为`link`原来的前后元素  
> 然后将`link.previous.next`与`link.previous`的指向修改为`newLink`。

`addAll(location:int, collection:Collection<? extends E>):boolean`  
`addAll(collection:Collection<? extends E>):boolean`

> 这两个方法的第二部分完全一样 - 循环添加元素。主要区别在于第一部分 - 查找`location`对应的元素。  
> 前者依然是**一分为二**的老方法，而后者则直接通过`voidLink`拿到尾元素。

`addFirst(object:E):void`  
`addLast(object:E):void`  
`add(object:E):boolean`

> 后两个是一样的，只不过来自于不同的接口/抽象类。  
> 顾名思义，一个是插入到头，一个是到尾。它们分别由对应的private方法`addFirstImpl(object:E):boolean`和`addLastImpl(object:E):boolean`来实现具体操作的。  
> 其实也可以调用`add(location:int, object:E):void`进行操作，但是因为它要首先查找元素，这样效率会低一些，所以另外写了2个方法，通过`voidLink`直接拿到原始的头/尾元素，进行操作。

`push(e:E):void`  
`offerFirst(e:E):boolean`  
`offerLast(e:E):boolean`

> 这三个是`Deque`接口里边定义的方法，前两个是一样的。其实对应的就是`addFirst(object:E):void`和`addLast(object:E):void`  

***`添加元素`总结***

> 1. 新建元素`newLink:Link`，并将其previous, next设置好
> 2. 修改原始`previous.next`的指向，将其指向`newLink`
> 3. 修改原始`next.previous`的指向，将其指向`newLink`

##### 删除元素
`remove(location:int):E`

> 和所有带有`location:int`参数的方法一样，第一部分是完全一样的 - **一分为二**查找指定元素`link`。  
> 然后接下来主要就是将`link.previous.next`与`link.next.previous`互相连接起来，从而使得`link`不再存在于这个引用链中。

`removeFirst():E`  
`remove():E`  
`pop():E`  
`removeLast():E`

> 前三个是一样的，都是删除头元素。  
> 和前边的添加元素的思路一样，分别有从头添加，从尾添加。它们分别由对应的private方法`removeFirstImpl(object:E):E`和`removeLastImpl(object:E):E`来实现具体操作的。  
> 如果要删除的元素不存在，会抛出异常`NoSuchElementException`。

`pollFirst():E`  
`poll():E`  
`pollLast():E`

> 前两个是一样的。  
> `pollXXX`与`removeXXX`的区别在于：  
> 如果要删除的元素不存在，前者返回`null`，后者会抛异常`NoSuchElementException`。

`clear():void`

> 与删除指定的一个元素相比，删除所有元素反而简单。即只需要将`voidLink.previous`和`voidLink.next`指向自身就可以了(或者直接赋值`null`)，因为`voidLink.data`本来就是`null`，所以就无需再处理了。  
> P.S. `size`置空。

`remove(object:E):boolean`  
`removeFirstOccurence(o:Object):boolean`  
`removeLastOccurence(o:Object):boolean`

> 前两个是一样的。  
> 刚开始自己想到的基本思路是 - 正向/反向遍历当前的`List`，找到符合条件的，将其删除。如果自己写遍历(循环)的话，比较麻烦，并且还要自己再更改引用。  
> 源码里边充分利用了**现有资源**：  
> 首先都构建一个`Iterator`，分别为正向和反向的，即`LinkIterator`和`ReverseLinkIterator`。  
> 然后调用private方法`removeOneOccurence(o:Object, iter:Iterator<E>)`。  
>> 在这个方法里边就比较简单了，`while`循环遍历`Iterator`，找到符合条件的，调用`iter.remove()`即可。  
>> 需要注意一点，`o:Object`有可能为`null`，所以要有两种情况的判断。

##### 读取元素
`get(location:int):E`

> 这个方法相对于添加或者删除指定元素更为简单，第一部分**一分为二**找到指定元素后，直接将其`data`返回即可。

`element():E`  
`getFirst():E`  
`getLast():E`

> 前两个是一样的。  
> 通过`voidLink`拿到头/尾元素(注意是否为空`List`的判定),然后返回其`data`。

`peek():E`  
`peekFirst():E`  
`peekLast():E`

> 前两个一样。  
> 这一组方法与`getXXX`的区别在于：  
> 如果为空`List`时，前者会抛出`NoSuchElementException`，而后者会返回`null`。

##### 更新元素
`set(location:int, object:E)`

> 同样的，首先**一分为二**找到`location`所对应的元素，然后将其`data`修改为`object`。

##### 其它方法
`clone:Object`

> 继承自`Object`的方法，返回的是浅拷贝。

`contains(object:Object):boolean`

> 对`LinkedList`做遍历，因为`object`可能为`null`或者`!null`，所以针对这两种情况进行遍历，如果找到，返回`true`。  
> 其实也可以模仿`remove`方法里，使用`Iterator`进行遍历，来判断是否包含指定元素。

`indexOf(object:Object):int`  
`lastIndexOf(object:Object):int`

> 具体的逻辑与`contains`相当像，只是在遍历时加上了计数器。  
> 前者是从头到尾遍历，后者是从尾到头遍历。  
> 同样，其实也可以利用`Iterator`来实现。

`descendingIterator():Iterator<E>`  
`listIterator(location:int):ListIterator<E>`

> 前者返回`ReverseLinkIterator`的实例  
> 后者返回`LinkIterator`的实例

`size():int`

> 直接返回`size`

`toArray():Object[]:Object[]`

> 根据`size`构建一个`Object[]`  
> 从头到尾遍历`LinkedList`，依次将元素添加到数组中

`toArray(contents:T[]):T[]`

> 与上个方法比，此方法含有类型信息。  
> 此方法的参数`contents:T[]`有两个作用：  
> 1. 提供类型信息  
> 2. 提供一个容器(有可能)  
> 
> 正如这个方法的帮助所描述的那样，如果传入的`contents:T[]`足够容纳`LinkedList`的所有元素，那么直接使用这个数组，否则会新建一个数组。  
> 然后就是从头到尾遍历`LinkedList`，将`data`添加到数组中。  
> 最后如果是复用数组，那么会在复制过来的`LinkedList`元素的结尾的下一个位置将值设置为`null`。

## ~~TODO~~
1. ~~自动序列化与反序列化时被`transient`修饰的变量是在哪里被***过滤***掉的？~~

	> 具体可以查看源代码`ObjectOutputStream.writeFieldValues`和`ObjectStreamClass.checkAndGetReflectionField`方法。

