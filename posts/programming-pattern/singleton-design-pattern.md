
单例模式是一种创建型设计模式， 让开发者能够保证一个类只有一个实例， 并提供一个访问该实例的全局节点，有助于协调系统整体的行为。

<!--more-->

## 简介

一个类只允许创建一个对象（或实例），那么这个类就是一个单例类，这种设计模式称作单例设计模式（Singleton Design Pattern），简称单例模式。

单例模式保证系统内存中只存在一个对象，非常节省系统资源，对于一些需要频繁销毁的对象，使用单例模式可以提高系统性能。

一个普通单例模式的实现方式主要是以下三个步骤：

1. 将单例类的构造方法定义为私有方法，禁止外部直接调用构造方法来实例化单例类的对象；
2. 在类的内部创建并保存类的唯一实例，并设置成私有变量，禁止外部直接调用这个实例变量；
3. 创建一个公开的静态方法，对外暴露类的唯一实例。

## 具体实现

### 饿汉式

饿汉式的实现方式就是，在类装载的期间，将类的实例初始化好，然后通过静态方法拿到实例化的对象。

对应的 Java 代码片段如下：

```java
public class Singleton {
    // 静态实例化
    private static final Singleton instance = new Singleton();

    // 构造器私有化
    private Singleton() {}

    // 公有静态方法，返回实例对象
    public static Singleton getInstance() {
        return instance;
    }
}
```

除了通过使用静态常量初始化实例的方式以外，还可以通过静态代码块的方式实现饿汉式单例模式。

对应的 Java 代码片段如下：

```java
public class Singleton {
    // 静态变量
    private static final Singleton instance;

    // 构造器私有化
    private Singleton() {}

    // 静态代码块
    static {
        instance = new Singleton();
    }

    // 公有静态方法，返回实例对象
    public static Singleton getInstance() {
        return instance;
    }
}
```

饿汉式的优点是，在类装载的时候就完成了实例化，避免了线程同步问题。

但是，这样的实现方式不支持延迟加载实例，如果从始至终未使用过这个实例，就会造成内存浪费。

并且，饿汉式在一些场景中无法使用：比如单例类实例的创建是依赖参数或者配置文件的，在通过 `getInstance()` 方法获取实例对象之前需要调用某个方法设置参数给对象实例，则这种方式将无法使用。

### 懒汉式

懒汉式相对于饿汉式的优势是支持延迟加载，可以在需要使用实例的时候才进行初始化。

对应的 Java 代码片段如下：

```java
public class Singleton {
    // 静态变量
    private static Singleton instance;

    // 构造器私有化
    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            // 实例不存在时初始化
            instance = new Singleton();
        }
        return instance;
    }
}
```

上述的实现方式是线程不安全的，如果有两个线程同时进入到 `getInstance()` 方法，并且正好都通过了判断语句，这时便会产生多个实例。通常不建议在生产环境中使用线程不安全的懒汉式创建单例类。

为了做到线程安全，可以给 `getInstance()` 方法加一把锁。

对应的 Java 代码片段如下：

```java
public class Singleton {
    // 静态变量
    private static Singleton instance;

    // 构造器私有化
    private Singleton() {}

    // 使用 synchronized 对方法进行加锁
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            // 实例不存在时初始化
            instance = new Singleton();
        }
        return instance;
    }
}
```

上述在 `getInstance()` 方法加锁的方式解决了线程不安全的问题，但是，由于加锁的粒度较大，实际的效率非常低。

如果这个单例类偶尔会被使用到，那这种实现方式还可以接受。但是，如果频繁地用到，那频繁加锁、释放锁则会出现并发度低的问题，造成性能瓶颈。

因此，也不建议在生产环境中使用线程安全的懒汉式创建单例类。

### 双重检测

饿汉式和懒汉式的实现方式都有一定的限制，而双重检测的实现方式是一种既支持延迟加载、又支持高并发的单例实现方式。

对应的 Java 代码片段如下：

```java
public class Singleton {
    // 静态变量
    private static Singleton instance;

    // 构造器私有化
    private Singleton() {}

    public static Singleton getInstance() {
        // 一次检测
        if (instance == null) {
            synchronized (Singleton.class) {
                // 二次检测
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

当有两个线程同时进入到 `getInstance()` 方法时，虽然会出现都通过第一次检查的判断语句，但是只会有一个线程获得锁并实例化对象，即使后续再有线程进入到同步代码块中，也会被第二次检查的判断语句挡在外面。

双重检测方式在多线程开发中常使用到，其优点是线程安全、支持延迟加载、效率较高。在实际开发中比较推荐使用这种方式实现单例模式。

### 静态内部类

静态内部类是一种比双重检测更加简单的实现方式。它有点类似饿汉式，但又能做到延迟加载。

对应的 Java 代码片段如下：

```java
public class Singleton {
    // 静态内部类
    private static class SingletonHolder {
        // 初始化实例
        private static final Singleton instance = new Singleton();
    }

    // 构造器私有化
    private Singleton() {}

    public static Singleton getInstance() {
        // 返回内部类的静态实例
        return SingletonHolder.instance;
    }
}
```

这种方式采用类装载机制来保证初始化实例时只有一个线程。

静态内部类方式在单例类被加载的时候并不会立即实例化，而是在调用 `getInstance()` 方法的时候，才会装载 SingletonHolder 类，从而实现单例类的实例化。

类的静态属性只会在第一次加载类的时候初始化，实例的唯一性、创建过程的线程安全性，都由 JVM 来保证。

所以，这种实现方法既保证了线程安全，又能做到延迟加载，效率也比较高，也是一种推荐使用的实现方式。

### 枚举

基于枚举类型的单例实现，是最简单的实现方式。

对应的 Java 代码片段如下：

```java
public enum Singleton {
    // 实例属性
    INSTANCE;

    public void doSomething() {
        // 通过以下方式调用此方法
        // Singleton.INSTANCE.doSomething();
    }
}
```

这种方式是通过 Java 枚举类型本身的特性，保证了实例创建的线程安全性和实例的唯一性，还能防止反序列化重新创建新的对象。

这种方式是[Effective Java中文版（第3版）](https://book.douban.com/subject/30412517/)作者提倡的方式，推荐在生产环境中使用。

## 深度理解

### 单例模式唯一性的范围

单例类只允许创建唯一对象（或实例），这里对象的唯一性范围指的是进程内只允许创建一个对象。

进程之间是不共享地址空间的，如果在一个进程中创建另一个进程，操作系统会给新进程分配新的地址空间，并且将老进程地址空间的所有内容重新拷贝一份到新进程的地址空间中，这些内容包括代码、数据。

所以，单例类在老进程中存在且只能存在一个对象，在新进程中也会存在且只能存在一个对象。而且，这两个对象不是同一个对象。

### 实现线程唯一的单例

“进程唯一”指的是进程内唯一，进程间不唯一。类比得知，“线程唯一”指的是线程内唯一，线程间不唯一。

其实，“进程唯一”的单例在同一个进程中的线程间唯一，若要做到“线程唯一”，主要是做到线程间保持不唯一。

实现线程唯一单例的代码很简单，可以通过一个键值对做关联存储，其中 key 是线程 ID，value 是对象。

对应的 Java 代码片段如下：

```java
import java.util.concurrent.ConcurrentHashMap;

public class Singleton {
    // 保证线程唯一的键值对
    private static final ConcurrentHashMap<Long, Singleton> instanceMap = new ConcurrentHashMap<>();

    // 构造器私有化
    private Singleton() {}

    public static Singleton getInstance() {
        Long currentThreadId = Thread.currentThread().getId();
        instanceMap.putIfAbsent(currentThreadId, new Singleton());
        return instanceMap.get(currentThreadId);
    }
}
```

### 实现集群唯一的单例

这里的集群表示进程集群，类比可知，“集群唯一”相当于进程间也唯一，即在不同的进程间共享同一个对象，不创建同一个类的多个对象。

实现集群唯一单例需要依赖到外部共享存储区：将单例对象序列化并存储到外部共享存储区，在使用这个单例对象的时候，需要先从外部共享存储区中将它读取到内存，并反序列化成对象，然后再使用，使用完成之后还需要再存储回外部共享存储区。

为了保证任何时刻在集群中都只有一份对象存在，一个进程在获取到对象之后，需要对对象加锁，避免其他进程再将其获取。

在进程使用完这个对象之后，还需要显式地将对象从内存中删除，并且释放对象的锁。

### 实现一个多例模式

“多例”指的是，一个类可以创建多个对象，但是个数是有限制的，同无限个有一些区别。

多例模式的实现也比较简单，通过一个键值对存储索引和对象之间的对应关系，并且需要控制对象的个数。

对应的 Java 代码片段如下：

```java
import java.util.Map;
import java.util.HashMap;
import java.util.Random;

public class Multipleton {
    // 限制实例数量
    private static final int COUNT = 3;

    // 存储对应关系的键值对
    private static final Map<Integer, Multipleton> instanceMap = new HashMap<>();

    // 饿汉式实现
    static {
        instanceMap.put(0, new Multipleton());
        instanceMap.put(1, new Multipleton());
        instanceMap.put(2, new Multipleton());
    }

    // 构造器私有化
    private Multipleton() {}

    // 公有静态方法，返回对应索引的实例对象
    public static Multipleton getInstance(Integer index) {
        return instanceMap.get(index);
    }

    // 公有静态方法，返回随机索引的实例对象
    public static Multipleton getRandomInstance() {
        Random random = new Random();
        Integer index = random.nextInt(COUNT);
        return instanceMap.get(index);
    }
}
```

## 总结

### 优点

单例模式的主要优点如下：

* 提供了对唯一实例的受控访问，封装性非常好
* 系统内存中只存在一个对象，可以节省系统资源
* 基于单例模式，可扩展实现多例类，既节省系统资源，又解决了由于单例模式共享过多有损性能的问题

### 缺点

单例模式的主要缺点如下：

* 单例模式对面向对象特性的支持不友好，违背了基于接口而非实现的设计原则
* 单例模式对代码的扩展性不友好，如要扩展则会导致改动较大
* 常规的单例模式不支持有参数的构造函数，只能通过其他方式改动单例类中的成员变量
* 对于有 GC 的编程语言，如果长时间不使用实例化的对象，则单例对象有可能会被销毁

### 适用场景

单例模式的适用场景如下：

* 单例模式主要针对需要频繁地创建和销毁的对象，可以理解成创建对象时耗时过多或耗费资源较大但又经常用到的对象。如工具类对象、频繁访问的数据库或文件对象
* 从业务概念上看，有些数据在系统中只应该保存一份，就比较适合设计成单例类。比如，系统的配置信息类
* 可以使用单例解决资源访问冲突的问题，单例模式可以只提供一个公共访问点

### 源码

在 JDK 中，`java.lang.Runtime` 是经典的单例模式，其用于与 Java 运行时环境进行交互。

Runtime 类是一个典型的饿汉式单例模式实现，如下是其的一些实现逻辑：

```java
public class Runtime {
    // 静态实例化
    private static final Runtime currentRuntime = new Runtime();

    private static Version version;

    // 静态方法获取静态实例
    public static Runtime getRuntime() {
        return currentRuntime;
    }

    // 构造器私有化
    private Runtime() {}
```

