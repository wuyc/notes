## 强引用（StrongReference）
强引用是使用最普遍的引用。如果一个对象具有强引用，那垃圾回收器绝不会回收它。
```java
Object o =new Object();
```

## 软引用（SoftReference）
内存空间足够，垃圾回收器不会回收它；  
内存空间不足，垃圾回收器就会回收它。  
软引用可用来实现内存敏感的高速缓存。
```java
String str = "abc";                                         // 强引用
SoftReference<String> softRef = new SoftReference<>(str);   // 软引用
```
当内存不足时，JVM首先将软引用中的对象引用置为null，然后通知垃圾回收器进行回收。
```java
if(JVM内存不足) {
    // 将软引用中的对象引用置为null
    str = null; // SoftReference 中的 str
    // 通知垃圾回收器进行回收
    System.gc();
}
```

## 弱引用（WeakReference）
垃圾回收器一旦扫描到具有若引用的对象，就会对其回收，不论内存空间足够与否。
```java
String str = "abc";
WeakReference<String> weakRef = new WeakReference<>(str);
```

## 虚引用（PhantomReference）
一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收器回收。虚引用用于监控对象的回收。（虚引用必须和引用队列一起使用）
```java
String str = "abc";
ReferenceQueue queue = new ReferenceQueue();
// 创建虚引用，要求必须与一个引用队列关联
PhantomReference pRef = new PhantomReference(str, queue);
```

## 引用队列（ReferenceQueue）
当引用的对象被回收后，对应的 Reference 对象会放到引用队列中。  
引用队列通过 Reference 类的构造方法传入。

## 总结
|引用类型|被垃圾回收时间|用途|生存时间|
|-|-|-|-|
|强引用|从来不会|对象的一般状态|JVM停止运行时终止|
|软引用|当内存不足时|对象缓存|内存不足时终止|
|弱引用|正常垃圾回收时|对象缓存|垃圾回收后终止|
|虚引用|正常垃圾回收时|跟踪对象的垃圾回收|垃圾回收后终止|

## 参考
- https://juejin.im/post/5b82c02df265da436152f5ad