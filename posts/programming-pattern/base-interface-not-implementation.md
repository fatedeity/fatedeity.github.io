# 基于接口而非实现编程


“基于接口而非实现编程”是一条比较抽象、泛化的设计思想，其的另一个表述是“基于抽象而非实现编程”。从这条设计思想中衍生的理解就是，越抽象、越顶层、越脱离具体某一实现的设计，越能提高代码的灵活性，越能应对未来的需求变化。

<!--more-->

## 抽象类和接口的区别

在面向对象编程当中，抽象类和接口是为抽象而生而的两个概念，在初学时特别容易搞混它们俩。

Java 既支持接口，也支持抽象类，这里主要拿 Java 的接口和抽象类做比较。简单地在 Java 中定义这两个概念就是，抽象类是包含抽象方法的类，接口是对行为的抽象。

### 抽象类

在 Java 中，抽象类仍然以 `class` 定义，并在此基础上增加 `abstract` 修饰，如下是抽象类的定义：

```java
[public|protected] abstract class ClassName {
    abstract void fun();
}
```

从定义上看，Java 中的抽象类就是用来继承的，没有被继承的抽象类没有任何实际的作用。而且，抽象类中的抽象方法只是起到一个限制的作用，并没有提供实际的方法体，这也要求子类去实现自己的方法体。

将抽象类的特征总结一下，大概有以下几点：

- 抽象类不允许被实例化，只能被继承
- 抽象类可以包含属性和方法，方法里既可以包含具体实现，也可以不包含具体实现，不包含具体实现的方法称为抽象方法
- 子类继承抽象类，必须实现抽象类的所有抽象方法

### 接口

在 Java 中，接口以 `interface` 定义，与 `class` 定义的类不同，如下是接口的定义：

```java
[public|protected] interface InterfaceName {
    void func();
}
```

接口实际上也可以包含变量和方法，但是，接口中的变量会被隐式地指定为 `public static final` 修饰的不可变量，接口中的方法会被隐式地指定为 `public abstract` 修饰的方法。

将接口的特性总结一下，大概有以下几点：

- 接口不能声明属性，可以声明的是静态变量
- 接口声明的方法不包含具体实现
- 类实现接口的时候，必须实现接口中声明的所有方法

### 区别

从上述对抽象类和接口的简单分析看，抽象类和接口的概念非常相似，从明面上看，其最大的区别就是，抽象类是用来继承的，接口是用来实现的。

从更深层次的角度上看，抽象类是不能被实例化的类，只能被子类继承，继承关系表示的是一种 is-A 的关系，接口表示的是一种 has-A 关系。

在使用时，抽象类可以定义一些公共的属性、方法，抽象方法用于声明子类继承的约束；接口的主要作用就是声明实现的协议，但是相比抽象类的优势就是一个类可以实现多个接口。

## 抽象类和接口的使用

### 抽象类的使用

首先，只能被子类继承的抽象类能解决代码复用的问题。

然后，抽象类表达的是一种抽象概念，适用于表示现实生活中的抽象概念。如狗是具体对象，动物则是抽象概念。

使用抽象方法，而非空的方法体，创建子类时就知道他必须要重写该方法，而不能忽略。

使用抽象类，类的使用者创建对象的时候，就知道他必须要使用某个具体子类，而不是抽象类本身。

使用抽象类提高了安全性，降低了开发者犯错的概率，是一种更优雅的编码方式。

抽象类更多的作用是引导使用者正确使用，避免被误用。

### 接口的使用

接口是对行为的一种抽象，相当于一组协议，更侧重于解耦。

调用者只需要关注抽象的接口，不需要了解具体的实现，具体的实现代码对调用者透明。接口实现了约定和实现相分离，可以降低代码间的耦合，提高代码的扩展性。

### 配合使用

如果抽象类只定义抽象方法，那抽象类和接口非常相似。但接口和抽象类在根本上是不同的，一个类可以实现多个接口，但只能继承一个类。

抽象类和接口是配合而不是替代，它们经常一起使用，接口声明能力，抽象类提供默认实现，实现全部或部分方法，一个接口经常有一个对应的抽象类。

比如，在 Java 类库中有以下关系：

- `Collection` 接口和对应的 `AbstractCollection` 抽象类
- `List` 接口和对应的 `AbstractList` 抽象类
- `Map` 接口和对应的 `AbstractMap` 抽象类

## 模拟抽象类和接口

### 通过抽象类实现接口

接口没有成员变量，没有方法实现，只有方法声明，实现接口的类必须实现接口中的所有方法。

只要满足上述几个特点，从设计的角度上讲，它就可以叫作接口。

在 Java 中，使用抽象类实现起来也比较简单，即抽象类只定义抽象方法即可，缺陷就是子类无法继承多个抽象类。

### 用普通类模拟接口

普通的类是可以包含具体实现的，这不符合接口的定义。但是，可以让类中的方法抛出 `NoSuchMethodError` 错误来模拟不包含实现的接口，并且强迫子类在继承这个父类时都去主动实现父类的方法，否则就会在运行时抛出异常。

为了避免普通的类被实例化，需要将这个类的构造函数声明成 `protected` 访问权限。

具体的代码实现如下：

```java
public class MockInterface {
    protected MockInterface() {}

    public void funcA() {
        throw new NoSuchMethodError();
    }
}
```

同样的，无论是使用抽象类还是普通类，实现的接口都无法满足接口的所有特性，这里也仅做一些了解。

## 基于接口而非实现编程

在软件开发中，最大的挑战之一就是需求的不断变化，因此，开发时一定要具有抽象意识、封装意识、接口意识。

越抽象、越顶层、越脱离具体某一实现的设计，越能提高代码的灵活性、扩展性、可维护性。

这个时候，接口的存在就非常必要了，通过使用接口定义实现类的协议，将约定和实现分离，做到了解耦的效果。

在定义接口的时候，一些注意事项就是：命名一定要足够通用，不能包含跟具体实现相关的字眼；另一方面，与特定实现相关的方法不要定义在接口中。

通常，越是不稳定的系统，越是要在代码的扩展性、维护性上下功夫。相反，某个系统特别稳定，在开发完成之后，基本不需要做维护，则没有必要为其扩展性、维护性投入不必要的开发时间。

