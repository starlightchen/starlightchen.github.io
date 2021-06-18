---
title: StringBuilder和StringBuffer
date: 2020-10-17 14:47 +0800
categories: [Java]
tags: [StringBuilder和StringBuffer]
---
# 层次结构

## StringBuffer

![StringBuffer的层次结构]({{site.url}}/assets/img/resource/StringBufferLayout.jpg)

## StringBuilder

![StringBuilder的层次结构]({{site.url}}/assets/img/resource/StringBuilderLayout.jpg)

<br/><br/>

# StringBuffer

StringBuffer 是一个线程安全类，它对append（）方法做了synchronized处理。

在AbstractStringBuilder中：
{% raw %}
```java
public AbstractStringBuilder append(String str){
    if(str == null)
        return appendNull();
    int len = str.length();
    ensureCapacityInternal(count + len);
    str.getChars(0, len, value. count);
    count += len;
    return this;
}

```
{% endraw %}
StringBuffer和StringBuilder均为AbstractStringBuilder类的实现，我们可以看到方法中执行两个步骤：
1. 对追加后的字符串进行长度预判断（此时未将str添加到原对象中），如果现存的value[]长度不满足追加后的长度，则以追加后的字符串长度进行newCapacity()
2. 调用String类中的getChars()方法，将字符真正添加到原字符串对象中去。

明显，AbstractStringBuilder.append()方法的操作不满足原子性，所以在多线程环境下可能会导致数组越界异常、结果数据不正确等非与其的问题。StringBuffer采用synchronized进行同步，所以可以保证在多线程环境下的线程安全。

## 构造函数
```java
StringBuffer() 
//value内容为空，并设置容量为16个字节；

StringBuffer(CharSequece seq)  
//使用seq初始化，容量在此基础上加16；

StringBuffer(int capacity) 
//设置特定容量；

StringBuffer(String str)  
//使用str初始化，容量str大小的基础上加16；
```

<br/><br/>

# StringBuilder

StringBuilder的原理和StringBuffer一样，不同之处在于StringBuilder不需要考虑线程安全。

## 构造函数

```java
StringBuilder() 
//value内容为空，并设置容量为16个字节；

StringBuilder(CharSequece seq)  
//使用seq初始化，容量在此基础上加16；

StringBuilder(int capacity) 
//设置特定容量；

StringBuilder(String str)  
//使用str初始化，容量str大小的基础上加16；
```



# 常用方法

两者均有以下方法：

```java
public StringBuffer append(String s)
//将指定的字符串追加到此字符序列。

public StringBuffer reverse()
 //将此字符序列用其反转形式取代。

public delete(int start, int end)
//移除此序列的子字符串中的字符.


public insert(int offset, int i)
//将 int 参数的字符串表示形式插入此序列中。

replace(int start, int end, String str)
//使用给定 String 中的字符替换此序列的子字符串中的字符。

String toString()
//返回此序列中数据的字符串表示形式。

String substring(int start, int end)
//返回一个新的 String，它包含此序列当前所包含的字符子序列。

String substring(int start)
//返回一个新的 String，它包含此字符序列当前所包含的字符子序列。

CharSequence subSequence(int start, int end)
//返回一个新的字符序列，该字符序列是此序列的子序列。

void setLength(int newLength)
//设置字符序列的长度。

int capacity()
//返回当前容量。

char charAt(int index)
//返回此序列中指定索引处的 char 值。

void ensureCapacity(int minimumCapacity)
//确保容量至少等于指定的最小值。

void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)
//将字符从此序列复制到目标字符数组 dst。

int indexOf(String str)
//返回第一次出现的指定子字符串在该字符串中的索引。

int indexOf(String str, int fromIndex)
//从指定的索引处开始，返回第一次出现的指定子字符串在该字符串中的索引。

int lastIndexOf(String str)
//返回最右边出现的指定子字符串在此字符串中的索引。

int lastIndexOf(String str, int fromIndex)
//返回 String 对象中子字符串最后出现的位置。

int length()
// 返回长度（字符数）。

void setCharAt(int index, char ch)
//将给定索引处的字符设置为 ch。
```


