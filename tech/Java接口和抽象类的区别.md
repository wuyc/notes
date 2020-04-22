## 接口
在接口中的方法都不能默认实现，接口中声明的成员变量都默认以`public static final`修饰，不可以使用其它修饰符修饰。  
但在 JDK8 以后，接口内的方法可以有默认实现，默认实现的方法使用`default`修饰。还可以在接口内定义静态方法。
同样的在 JDK8 以后，接口内只有一个抽象方法，我们称这个接口为函数式接口，比如`Runnable`接口就是函数式接口。
```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```
如果是函数式接口，我们可以使用 Lambda 表达式简化我们的代码。
```java
public class Main {
    public static void main(String[] args) {
        new Thread(() -> {
            System.out.println("Hello World");
        }).start();
    }
}
```

## 抽象类
抽象类中的成员变量和普通类一样，可以公有也可以私有，可以静态也可以非静态。  
抽象类中的方法可以有实现，也可以不实现。

---

一个普通类使用`extends`继承一个抽象类或者普通类，不能`extends`多个类；  
使用`implements`实现一个接口，可以`implements`多个接口。

## 参考
- https://www.yiibai.com/java/difference-between-abstract-class-and-interface.html