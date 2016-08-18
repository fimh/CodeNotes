# Enumeration

Android Version: 22  
Time: 2016/07/26  
Tag: Finished  

## 分析
`Enumeration`接口是一个历史遗留的接口，早在JDK 1.0时代就已经定义。

它不是一个数据结构，它的作用是**从一个数据接口得到连续数据**。
即**它是早期的迭代器 - `Iterator`**。

### 方法分组
方法较少，无需分组。

它定义了2个方法(和迭代器非常相似):

`hasMoreElements():boolean`

> 相当于`Iterator.hasNext`

`nextElement():E`

> 相当于`Iterator.next`

由于这个接口已是遗留接口，所以我们目前在Android Framework中只能找到一个类`StringTokenizer`实现了这个接口。
而这个类也早在JDK 1.0时代就有了。

## ~~TODO~~
1. ~~与Iterator相比较，有什么异同？~~

	> 很简单，没有`remove`接口，其它的与`Iterator`一样。