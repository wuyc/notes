Java package 全部使用小写字母，英语单词不使用复数，如工具包使用`util`，不使用`utils`。
***
尽量减少多个判断语句或循环语句的嵌套。可以把代码抽离到另外的方法中、或者尽早`return`来减少嵌套代码。
***
### 数组命名
```java
int i[] // don't do
int[] i // do
```
***
### 判断对象是否为 null
```java
if (obj == null) {}
if (null == obj) {} // better
```
`null`写在前面可以避免漏写一个等号而变成赋值语句。
***
### equas 方法的使用
```java
str.equas("hello")
"hello".equas(str) // better
```
`"hello"`写在前面可以防止空指针错误。
***
### DAO 层方法命名风格（与`SQL`语句保持一致）
- 增：以`insert`开头
- 删：以`delete`开头
- 改：以`update`开头
- 查：以`select`开头，查询单个对象使用单数，多个对象使用复数。
- 统计：以`count`开头
### Service 层方法命名风格
- 增：以`create`开头
- 删：以`remove`开头
- 改：以`update`开头
- 查：以`get`开头，查询单个对象，对象名使用单数；
以`list`开头，查询多个对象，对象名使用复数。
- 统计：以`count`开头