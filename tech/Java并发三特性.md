特性|volatile|synchronized|Lock|Atomic
:-:|:-:|:-:|:-:|:-:
原子性|无法保障|可以保障|可以保障|可以保障|
可见性|可以保障|可以保障|可以保障|可以保障|
有序性|一定程度保障|可以保障|可以保障|无法保障|

Java 里**只有** volatile 变量能实现禁止指令重排。

synchronized 块里的非原子操作依旧可能发生指令重排。

*双重检查锁定模式需要使用 volatile 修饰单例对象*

参考：
- https://juejin.im/post/5d5c9fbce51d4561cd246641
- https://www.zhihu.com/question/337265532
- https://zh.wikipedia.org/wiki/双重检查锁定模式