
面向对象是一种软件开发的编程范式。其概念和应用已超越了程序设计和软件开发，扩展到如数据库系统、交互式界面、应用结构、应用平台、分布式系统、网络管理结构、CAD 技术、人工智能等领域。

<!--more-->

## 面向对象

### 面向对象编程

面向对象编程（Object Oriented Programming, OOP）是一种编程范式或编程风格，它以类或对象作为组织代码的基本单元，并以封装、继承、多态这三个特性作为代码设计和实现的基石。

面向对象的类是描述了一组有相同特征（属性）和相同行为（方法）的一组对象的集合；对象是类的一个实例，拥有自己的状态和行为。

### 面向对象编程语言

面向对象编程语言（Object Oriented Programming Language, OOPL）是支持以类或对象的语法机制，并有现成的语法机制，能方便地实现面向对象编程的三大特性（封装、继承、多态）的编程语言。

### 面向对象编程设计和分析

面向对象分析（Object Oriented Analysis, OOA）就是要搞清楚是什么、为什么要做。

面向对象设计（Object Oriented Design, OOD）就是要搞清楚由谁来做、什么时候做、在哪里做、怎么做、做到怎样的程度。

设计和分析就是一个将想法付诸于实际的过程，因此，其中的每一步都非常重要，影响到程序这项工程的维护。

### 面向对象的优点

面向对象程序设计有以下优点：

- 可重用性：代码重复可用，减少代码量，提高开发效率
- 可扩展性：新的功能可以很容易地加入到系统中来，便于软件的修改
- 可管理性：能够将功能和数据结合，方便管理

## 核心特性

### 封装

#### 封装的含义

封装也叫作信息隐藏或数据访问保护。详细地说，就是数据被保护在抽象数据类型的内部，尽可能对外隐藏内部的细节，只保留一些统一的方法供外部使用。

比如说，对于一个钱包类，里面有余额、币种这两个属性，通常是不允许外部直接更新余额或者直接更新币种，而是仿照现实交易的找补零钱的方式，对外提供一个找补零钱的方法，在这个方法中根据提供的参数来更新余额和币种，这样可以保证数据的一致性。

#### 封装的优点

封装具有以下优点：

- 提高了代码的安全性，阻止外部随意修改，避免造成数据不一致
- 提高了代码的易用性，简化外部调用，便于扩展和协作
- 提高了代码的可维护性，封装内部细节，方便修改内部代码

### 继承

#### 继承的含义

继承指的是子类拥有父类的全部特征和行为，用来表示类之间 is-A 的关系。

比如说，汽车是一种交通工具，汽车会有交通工具的一些特性和功能，交通工具狭义上指一切人造的用于人类代步或运输的装置，汽车就属于这类工具中的一种。

#### 单继承和多继承

从继承的多向性来讲，继承可分为两种模式：单继承和多继承。

单继承表示一个子类只能继承一个父类，多继承表示一个子类可以继承多个父类。从现实世界的角度上看，多继承更符合现实，比如说，猫既是哺乳动物，又是爬行动物。

但是，从软件开发的角度上看，单继承的优点在于层次结构清晰，设计上更容易把握；多继承可以让子类具备多个父类的特征，拥有更丰富的方法，但是多继承会出现菱形继承的问题。

简单地理解菱形继承就是，假设子类 B 和子类 C 都继承自父类 A，且都重写了父类 A 中的方法 func，而孙子类 D 同时继承了子类 B 和子类 C，对于方法 func 而言，孙子类 D 会出现歧义。

#### 继承的优缺点

继承最大的好处就是代码复用，子类可以直接重用父类中的代码，避免代码重复写多遍。

但是过度地使用继承会导致代码可读性、可维护性变差，有可能出现“父类、父类的父类……”的代码。

通常，可以在层次简单、关系不复杂的时候使用继承，反之使用组合代替继承。

### 多态

#### 多态的含义

多态指的是为不同数据类型的实体提供统一的接口，或使用一个单一的符号来表示多个不同的类型。

#### 通过继承实现

多态可以通过继承的方式实现，子类继承父类之后，并重写了父类的方法，在初始化子类的对象时，可以将对象定义为父类的数据类型，这时的对象调用的会是重写后的子类方法。

如下述代码所示：

```java
package cn.fatedeity.designpattern.polymorphism;

/**
 * 通过继承实现多态
 */
public class ExtendCase {
    private static void test(Base base) {
        System.out.println(base.getSize());
    }

    public static void main(String[] args) {
        Base baseAddOne = new BaseAddOne();
        // 1
        test(baseAddOne);

        Base baseAddTwo = new BaseAddTow();
        // 2
        test(baseAddTwo);
    }
}

class BaseAddTow extends Base {
    @Override
    public int getSize() {
        return this.size + 2;
    }
}

class BaseAddOne extends Base {
    @Override
    public int getSize() {
        return this.size + 1;
    }
}

class Base {
    protected int size = 0;

    public int getSize() {
        return size;
    }
}
```

#### 通过接口实现

多态还可以通过接口的方式实现，当接口被实现之后，在初始化实现类的对象时，可以直接将这个对象定义为接口类型，这时的对象调用的会是实现类的方法。

如下述代码所示：

```java
package cn.fatedeity.designpattern.polymorphism;

/**
 * 通过接口实现多态
 */
public class ImplementsCase {
    private static void test(InterfaceBase base) {
        System.out.println(base.toString());
    }

    public static void main(String[] args) {
        InterfaceBase interfaceOne = new InterfaceOne();
        // This is InterfaceOne
        test(interfaceOne);

        InterfaceBase interfaceTwo = new InterfaceTow();
        // This is InterfaceTwo
        test(interfaceTwo);
    }
}

class InterfaceTow implements InterfaceBase {
    @Override
    public String toString() {
        return "This is InterfaceTwo";
    }
}

class InterfaceOne implements InterfaceBase {
    @Override
    public String toString() {
        return "This is InterfaceOne";
    }
}

interface InterfaceBase {
    String toString();
}
```

#### 通过鸭子类型实现

所谓的鸭子类型，指的是只关心事物的外部行为而非内部结构，即不关心对象是什么类型，只关心该对象是否拥有指定方法。

通过鸭子类型实现多态更加灵活，不需要类之间有继承、接口实现的关系，只需要它们同时定义了相同的方法即可。如下述的 Python 代码所示：

```python
class Logger:
    def record(self):
        print('I write a log into file.')

class DB:
    def record(self):
        print('I insert data into db.')

def test(recorder):
    recorder.record()

def demo():
    logger = Logger()
    # I write a log into file.
    test(logger)

    db = DB()
    # I insert data into db.
    test(db)
```

#### 多态的意义

对于第一个例子的代码，仅用一个 `test()` 方法即可测试 Base 类的子类，即使要新增一个 BaseAddThree 子类，同样不需要更改 `test()` 方法，仅需重写自己的 `getSize()` 方法即可，这里提高了代码的扩展性。

同样的，仅用一个 `test()` 即可完成所有的测试，而不需对每一个子类都写一遍测试代码，这里显然提高了代码的复用性。

除此之外，多态还是很多设计模式、设计原则、编程技巧的代码实现基础。

## 面向对象和面向过程

### 为什么使用面向对象而不是面向过程？

面向过程是一种流程化的思维模式，面向对象是一种自底向上的抽象化的思维模式。

相比之下，面向对象有以下优势：

- 面向对象编程更加能够应对大规模复杂程序的开发，它提供了一种清晰的、模块化的代码组织方式
- 面向对象编程的的三大特性提高了代码的易维护性、扩展性、复用性，并且大部分设计模式都以面向对象为基础
- 面向对象编程语言更加人性化、更加高级、更加智能，面向过程的流程化是一种计算机思维方法，而面向对象的抽象化是一种人类思维方法

### 违反面向对象编程风格的典型代码设计

- 滥用 `setter()` 方法和 `getter()` 方法使封装失去作用
- 定义大而全的 `Constants` 类、`Utils` 类也破坏了封装特性
- MVC 模式是基于贫血模型的开发模式，数据和操作分开，是彻底的面向过程编程风格

