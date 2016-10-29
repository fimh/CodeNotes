# Hashtable

Android Version: 22  
Time: 2016/10/29  
Tag: Finished  

## 分析
前边分析过HashMap，再来分析这个类就会方便许多。
在`Hashtable`这个类的注释里边，描述比较简单，只提到了2点：
1. 这个类是`Map`接口的同步实现
2. 这个类不支持键/值为null(如果要使用null作为键/值，那么使用`HashMap`或者`LinkedHashMap`)

为了兼容之前的`Dictionary`抽象类，这个类继承了它，同时也实现了`Map`接口。
(前边分析`Dictionary`的时候，我们已经知道，这个抽象类其实就是一个接口，而且已经不再推荐使用了)。

在看具体代码之前，我首先看了一遍public方法的签名，结果发现所有的public方法(构造方法除外)都有`synchronized`，也就是说这个类实现同步的方式很简单 - 在每个方法签名里加上`synchronized`关键字。

那么，抛开同步的`synchronized`，内部的实现是不是和HashMap一样呢？我们带着这个问题来看代码。

## 与`HashMap`比较
### 变量
少了`entryForNullKey`

> `Hashtable`不支持键/值为null。

### 方法
少了`init()`
多了`rehash()`

> 这两个方法都是空方法，主要是出于设计上考虑而编写的方法。

内部实现

> 两者内部的算法是基本一致的，但是因为`Hashtable`不支持键/值为null，所以在下面的这些方法里边都去掉了对键/值为null的操作：
>> `get(key:Object)`
>> `containsKey(key:Object)`
>> `containsValue(value:Object)`
>> `put(key:K, value:V)`
>> `remove(key:Object)`

多了`contains(value:Object)`

> 该方法其实是`containsValue(value:Object)`方法的一个快捷方式。

## ~~TODO~~