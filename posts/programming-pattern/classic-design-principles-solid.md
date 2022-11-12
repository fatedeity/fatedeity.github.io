# 经典设计原则 - SOLID


SOLID 原则是面向对象编程和面向对象设计的五个基本原则。当这些原则被一起应用时，它们使得一个程序员开发一个容易进行软件维护和扩展的系统变得更加可能。

<!--more-->

SOLID 设计原则包含以下 5 种原则：

- 单一职责原则（Single Responsibility Principle, SRP）
- 开闭原则（Open Closed Principle, OCP）
- 里式替换原则（Liskov Substitution Principle, LSP）
- 接口隔离原则（Interface Segregation Principle, ISP）
- 依赖反转原则（Dependency Inversion Principle, DIP）

## 单一职责原则

### 理解

单一职责原则的描述是，一个类或者模块只负责完成一个职责（或功能）。当然，单一职责原则不止是可以针对于模块或类，对于很多粒度都有效果，如函数、类、接口、模块等等，模块通常由多个类组成。

职责可以指模块变化的原因，从这个角度理解，单一职责原则表示不要存在超过一个导致模块变更的原因。

需要注意的是，不同的应用场景、不同阶段的需求背景、不同的业务层面，对同一个类的职责是否单一，可能会有不同的判定结果。

### 优点

遵循单一职责原则，将会有以下的优点：

- 提高代码的可维护性：职责越少，复杂度越低，可读性更好，可维护性就更高
- 降低代码变更的风险：职责越多，代码变更的可能性就越高，变更带来的风险也就越大

### 最佳实践

在实际开发中，出现以下现象有可能违反了单一职责原则：

- 模块的变量、属性或代码行数过多
- 模块的内部对外部依赖过多
- 模块的私有方法过多
- 难以给模块取一个合理的名称
- 模块的大部分操作只针对几个属性

如出现上述情况，则需要判断是否对代码做职责分离，以遵循单一职责原则，最终应以提高内聚、降低耦合、保证代码的可维护性为主。

## 开闭原则

### 理解

开闭原则的描述是，软件实体（模块、类、方法等）应该“对扩展开放、对修改关闭”。

详细的解释就是，添加一个新的功能时，在已有代码基础上扩展代码（新增模块、类、方法等），而非修改已有代码（修改模块、类、方法等）。更宽松的理解是以最小的修改代码的代价来完成新功能的开发。

### 优点

遵循开闭原则，将会有以下的优点：

- 减少测试范围：修改的代码范围越小，涉及的测试范围越小，未改动的测试代码仍能正常运行
- 降低维护成本：软件规模越大、寿命越长，则软件的维护成本越高

### 最佳实践

若要做到“对扩展开发、对修改关闭”，有以下几点需要注意：

- 时刻具备扩展意识、抽象意识、封装意识，多花时间设计代码结构，事先留好扩展点
- 大部分经典设计模式都是为了解决代码的扩展性问题而总结出来的，开闭原则是它们一个重要的评价依据

## 里式替换原则

### 理解

里式替换原则的描述是，子类对象能够替换程序中父类对象出现的任何地方，并且保证原来程序的逻辑行为不变及正确性不被破坏。

从代码实现上看，面向对象的多态和里式替换原则有点类似，但是它们的关注点不一样：里式替换原则是用来指导继承关系中子类该如何设计，子类的设计要保证在替换父类的时候，不改变原有程序的逻辑以及不破坏原有程序的正确性。

### 优点

遵循里式替换原则，将会有以下的优点：

- 实现有意义的继承：保证了父类的复用性，也降低了系统出错误的故障，防止误操作，同时也不会破坏继承的机制
- 增强程序的健壮性：不同的子类可以完成不同的业务逻辑，即使增加子类也能保持非常好的兼容性

### 最佳实践

通常，需要注意以下违反里式替换原则的代码：

- 子类违背父类声明要实现的功能，如将加法改成减法
- 子类违背父类对输入、输出、异常的约定，如同一情况抛出的异常不同等
- 子类违背父类注释中所罗列的任何特殊声明

## 接口隔离原则

### 理解

接口隔离原则的描述是，接口的调用者或使用者不应该被强迫依赖它不需要的接口。

通过对接口的理解不同，接口隔离原则有以下三种理解：

1、如果把“接口”理解成一组接口集合，可以是某个微服务的接口，也可以是某个类库的接口等。如果存在部分接口只被部分调用者使用，就需要将这部分接口隔离出来，单独给这部分调用者使用，而不强迫其他调用者也依赖其他不会用到的接口。

2、如果把“接口”理解成单个 API 接口或函数，部分调用者只需要其中的部分功能，则需要将这个函数拆分成更细粒度的多个函数，让调用者只依赖它需要的那个细粒度函数。

3、如果把“接口”理解成 OOP 中的接口，也可以理解成为面向对象编程语言中的接口语法，那接口的设计要尽量单一，不要让接口的实现类和调用者依赖不需要的接口函数。

接口隔离原则和单一职责原则有点类似，但接口隔离原则更侧重于接口的设计，通常是通过调用者如何使用接口来定义这个接口的设计是否足够职责单一。

### 优点

遵循接口隔离原则，将会有以下的优点：

- 高内聚，低耦合：拆分成更小粒度的接口，减少对外的交互，预防外来的变更，提高系统的灵活性和可维护性
- 可读性高，易于维护：合理的接口拆分粒度能保证系统的稳定性，减少项目工程的代码冗余

### 最佳实践

采用接口隔离原则对接口进行约束时，要注意以下几点：

- 接口尽量小，但是要有限度。定义过小，则会造成接口数量过多，使设计复杂化；定义多大，灵活性降低
- 每个项目和产品都有选定的环境因素，环境不同，接口拆分的标准就不同，深入了解业务逻辑

## 依赖反转原则

### 理解

依赖反转原则也被叫作依赖倒置原则，其含义是：高层模块不要依赖底层模块，高层模块和底层模块应该通过抽象来互相依赖；抽象不要依赖具体实现细节，具体实现细节依赖抽象。

Tomcat 是运行 Java Web 应用程序的容器，编写的 Web 应用程序代码只需要部署在 Tomcat 容器中下，便可被 Tomcat 容器调用执行。在这里，Tomcat 容器就是高层模块，Web 应用程序就是底层模块。Tomcat 容器和 Web 应用程序没有直接的依赖关系，而是通过 Servlet 规范实现互相依赖，而 Servlet 规范也不会依赖具体的实现细节，而是 Tomcat 和 Web 应用程序依赖 Servlet 规范。

### 控制反转

控制反转（Inversion Of Control, IoC）指的是将程序员自己对程序执行流程的控制反转成通过框架控制。控制反转并不是一种具体的设计技巧，而是一种笼统的设计思想，一般用来指导框架层面的设计。

实现控制反转主要有两种方式：依赖注入和依赖查找。两者的区别在于，前者是被动的接收对象，在类 A 的实例创建过程中即创建了依赖的 B 对象，通过类型或名称来判断将不同的对象注入到不同的属性中，而后者是主动索取相应类型的对象，获得依赖对象的时间也可以在代码中自由控制。

### 依赖注入

依赖注入（Dependency Injection, DI）是一种具体的编码技巧。

其详细概括就是：不通过 `new` 的方式在类的内部创建依赖对象，而是将依赖的类对象在外部创建好之后，通过构造函数、函数参数等方式传递（或注入）给类使用。

一个简单的依赖注入代码例子如下：

```java
package cn.fatedeity.designpattern.philosophy;

/**
 * 依赖注入案例
 */
public class DependencyInjectionCase {
    private MessageSender messageSender;

    public DependencyInjectionCase(MessageSender messageSender) {
        this.messageSender = messageSender;
    }

    public void sendMessage(String phone, String message) {
        this.messageSender.send(phone, message);
    }

    public static void main(String[] args) {
        MessageSender smsSender = new SmsSender();
        DependencyInjectionCase dependencyInjectionCase0 = new DependencyInjectionCase(smsSender);
        // SmsSender sms send sms message
        dependencyInjectionCase0.sendMessage("sms", "sms message");

        MessageSender inboxSender = new InboxSender();
        DependencyInjectionCase dependencyInjectionCase1 = new DependencyInjectionCase(inboxSender);
        // InboxSender inbox send inbox message
        dependencyInjectionCase1.sendMessage("inbox", "inbox message");
    }
}

class InboxSender implements MessageSender {
    @Override
    public void send(String phone, String message) {
        System.out.println("InboxSender " + phone + " send "+ message);
    }
}

class SmsSender implements MessageSender {
    @Override
    public void send(String phone, String message) {
        System.out.println("SmsSender " + phone + " send "+ message);
    }
}

interface MessageSender {
    void send(String phone, String message);
}
```

### 优点

遵循依赖反转原则，将会有以下的优点：

- 查询依赖和应用代码分离，大量降低工厂类和单例类的数量，代码层次更加清晰
- 没有侵入性，无须依赖容器的 API，也无须实现一些特殊接口

### 最佳实践

通过依赖注入提供的扩展点，简单配置一下所有需要的类及其类之间依赖关系，就可以实现由框架来自动创建对象、管理对象的生命周期、依赖注入等功能。

现成的依赖注入创建有很多，比如 Google Guide、Java Spring、Pico Container、Butterfly Container 等。

