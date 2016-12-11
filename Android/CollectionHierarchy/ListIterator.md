# ListIterator

Android Version: 22  
Time: 2016/12/11  
Tag: Finished  

## 分析
`ListIterator`用于`List`数据结构进行遍历。

`ListIterator`与其父类`Iterator`的关系和`List`与`Collection`的关系类似：  
* 增加了一些与位置相关的方法(由于有了顺序，那么遍历时我们可以双向进行)
* 在遍历`ListIterator`时还可以添加或者替换元素

### 方法
#### 与位置相关的方法
`hasPrevious():boolean`
`previous():E`
`previousIndex():int`

> 与向前遍历相关的几个新增的方法

`nextIndex():int`

> 新增的向后遍历的一个方法

#### 更改数据
`add(object:E):void`

> 将`object`添加到`next`与`previous`之间，即该元素为`previous object`

`set(object:E):void`

> 替换掉上一次调用`next`或`previous`返回的`object`

## ~~TODO~~
