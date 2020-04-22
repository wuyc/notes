## 集合框架图
![](https://user-images.githubusercontent.com/40560751/77065362-1e684580-6a1c-11ea-9fa8-e6e06ba7aded.png)
- Iterator
Java 集合类的父接口，提供了`hasNext()` `next()` `remove()`等方法遍历集合。`Map`接口没有实现该接口。
- Collection
`Collection`接口实现了` Iterator`接口，大部分的集合类都实现了`Collection`接口。`Collection`下面有两个重要的接口，分别是`List`和`Set`。
- Map
`Map`接口下的实现类都是以键值对的方式进行存储，如`HashMap`、`TreeSet`等。

---

## List
- `ArrayList`：**线程不安全**。内部使用一个`Object[]`数组进行存储，数组初始容量为10，当数组大小不足时容量扩大1.5倍。
- `LinkedList`：**线程不安全**。使用双向链表实现。它还可以当做队列或栈使用。关于栈或队列，现在的首选是`ArrayDeque`，它有着比`LinkedList`（当作栈或队列使用时）有着更好的性能。
> 如果需要线程安全，可以使用`Vector`（内部使用`synchronized`同步锁，现在已经不推荐使用）、`Collections.synchronizedList()`、`CopyOnWriteArrayList`（适合读多写少）。
## Set（不包含重复元素）
- `HashSet`：**线程不安全**。内部使用`HashMap`的键进行存储。元素可以为`null`。
- `LinkedHashSet`：**线程不安全**。保持插入顺序。
- `TreeSet`：**线程不安全**。可以排序。
> 如果需要线程安全，可以使用`Collections.synchronizedSet()`、`CopyOnWriteArraySet`（适合读多写少）。
## Map
- `HashMap`：**线程不安全**。根据`key`的`hashCode`计算出`HashMap`内数组的下标，然后把`value`存在这个数组（数组默认大小为16）里，如果数组内有多个元素，则使用链表的方式存储，如果链表长度超过8个，链表会转换成红黑树。
- `LinkedHashMap`：**线程不安全**。保持插入顺序。
- `TreeSet`：**线程不安全**。基于红黑树。可以排序。
- `Hashtable`：**线程安全**。线程安全使用`synchronized`关键字实现，`key`和`value`为`null`时抛空指针异常。
- `ConcurrentHashMap`：**线程安全**。性能比`Hashtable`好，因为内部锁的控制粒度更小。
> 如果需要线程安装，可以使用`Hashtable`、`Collections.synchronizedMap()`、`ConcurrentHashMap`。

## 参考
- https://frank-lam.github.io/fullstack-tutorial/#/JavaArchitecture/02-Java集合框架