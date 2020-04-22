## 浅克隆
> 浅克隆只复制当前对象的所有基本数据类型，以及相应的引用变量，但没有复制引用变量指向的实际对象，也就是只复制了引用变量的内存地址。

重写`Object`的`clone`方法，然后实现`Cloneable`接口。被克隆的类必须实现`Cloneable`接口，否则如果我们将在对象上调用`clone()`时，JVM将抛出`CloneNotSupportedException`。

### Main.java
```java
public class Main {
    public static void main(String[] args) {
        Person source = new Person();
        Person target = null;
        try {
            target = source.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        System.out.println("被克隆对象: " + source.hashCode() + "\n" +
                "被克隆对象内的dog属性: " + source.getDog().hashCode());
        System.out.println("===============================");
        System.out.println("克隆出来的对象: " + target.hashCode() + "\n" +
                "克隆出来的对象内的dog属性: " + target.getDog().hashCode());
    }
}
```
### Person.java
```java
public class Person implements Cloneable {
    private Dog dog;

    public Dog getDog() {
        return dog;
    }

    public Person() {
        this.dog = new Dog();
    }

    @Override
    protected Person clone() throws CloneNotSupportedException {
        return (Person) super.clone();
    }
}
```
### 输出
```
被克隆对象: 1163157884
被克隆对象内的dog属性: 1956725890
===============================
克隆出来的对象: 356573597
克隆出来的对象内的dog属性: 1956725890
```
我们发现两个对象的`dog`属性在内存中的标识并没有改变，因为浅克隆只拷贝了引用变量所指向堆内存的内存地址。

## 深克隆
> 深克隆彻底复制了当前对象，此对象与母对象在任何引用路径上都不存在共享的实例对象。也就是说深克隆把所有引用变量所指向的变量都拷贝了一份。

先把对象序列化到流中，然后再把对象从流中取出来，取出来的对象和原来的对象就没有联系了，引用变量所指向的地址也和原来的对象不同，这样就达到了深克隆的目的。不过这种方式的效率会比较低。
除了通过序列化成流进行深克隆，还可以通过手动设置属性的值实现克隆。
下面演示通过序列化成流进行深克隆。

### Main.java
```java
public class Main {
    public static void main(String[] args) {
        Person source = new Person();
        Person target = (Person) CloneUtils.clone(source);
        System.out.println("被克隆对象: " + source.hashCode() + "\n" +
                "被克隆对象内的dog属性: " + source.getDog().hashCode());
        System.out.println("===============================");
        System.out.println("克隆出来的对象: " + target.hashCode() + "\n" +
                "克隆出来的对象内的dog属性: " + target.getDog().hashCode());
    }
}
```
### Person.java
```java
public class Person implements Serializable {
    private Dog dog;

    public Dog getDog() {
        return dog;
    }

    public Person() {
        this.dog = new Dog();
    }
}
```
### Dog.java
```java
public class Dog implements Serializable {
    private String name;

    public Dog() {
        this.name = "大黄";
    }
}
```
### CloneUtils.java
```java
public class CloneUtils {

    public static Object clone(Object obj) {
        Object cloneObj = null;
        try {
            //写入字节流
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            ObjectOutputStream obs = new ObjectOutputStream(out);
            obs.writeObject(obj);
            obs.close();

            //分配内存，写入原始对象，生成新对象
            ByteArrayInputStream ios = new ByteArrayInputStream(out.toByteArray());
            ObjectInputStream ois = new ObjectInputStream(ios);

            //返回生成的新对象
            cloneObj = ois.readObject();
            ois.close();

        } catch (Exception e) {
            e.printStackTrace();
        }
        return cloneObj;
    }
}
```
### 输出
```
被克隆对象: 1836019240
被克隆对象内的dog属性: 325040804
===============================
克隆出来的对象: 363771819
克隆出来的对象内的dog属性: 2065951873
```
由输出结果可以看出引用变量`dog`的值已经指向了另外的一个堆内存地址，即通过序列化成流实现了深克隆。