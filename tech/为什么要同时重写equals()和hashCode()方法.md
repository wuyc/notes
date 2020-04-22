## equals()
`equals()`判断两个类的内容是否相等，它是基类`Object`的一个方法，默认实现如下：
```java
public boolean equals(Object obj) {
    return (this == obj);
}
```
默认使用 == 判断两个对象是否指向同一引用，相当于判断两个对象的内存地址是否相等。

## hashCode()
`hashCode()`也是基类`Object`的方法，它是 native 方法，默认用 C/C++ 实现。`hashCode()`可以获取当前对象的哈希值，它是根据当前对象的内存地址经过计算得出来的一个 int 值。

---

**当一个对象在基于 Hash 集合中当做键时，要同时重写`equals()`和`hashCode()`方法。比如 HashMap，HashSet 等等。**

当调用`HashMap.put()`方法时，先会调用键的`hashCode()`方法获取键的哈希值，然后把获取过来的哈希值经过 Hash 算法运算，再根据运算出来的结果得到`HashMap`内数组的下标，put 进来的值就存在这个下标上。如果又 put 进来一个键的`hashCode`相等，`equals()`不相等的数据，就会在当前数组的位置创建一个链表，后 put 进来的数据到放链表头，原来的数据向后移动。

当调用`HashMap.get()`方法时，通过对象的`hashCode()`方法获取哈希值，再把哈希值经过运算得出数组下标，下标上的数据如果就一个数据，就取出来，如果是个链表，就遍历这个链表，用`equals()`判断哪个相等就取出链表上的数据。

## 参考
- https://www.cnblogs.com/JavaArchitect/p/10474448.html
- https://zh.wikipedia.org/zh-cn/哈希表
- https://baike.baidu.com/item/hash/390310
- https://www.cnblogs.com/yangming1996/p/7997468.html