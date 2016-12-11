# Iterator

Android Version: 22  
Time: 2016/12/11  
Tag: Finished  

## 分析
其实前面已经分析过该接口的一个实现类了 - `HashMap.HashIterator`

### 方法
`hasNext():boolean`

> 见名知义。是否还有下一个元素。

`next():E`

> 返回下一个元素，并且在内部移动Iterator的指针。

`remove():void`

> 删除`next`返回的最后一个元素，注意该删除只能在`next`之间使用。

## ~~TODO~~
