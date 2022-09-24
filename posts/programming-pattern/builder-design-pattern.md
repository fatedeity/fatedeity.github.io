
顾名思义，如何建房子一样，建造者模式可以将复杂对象的建造过程抽象出来，使用相同的构建过程可以构造出不同表现的对象。

<!--more-->

## 简介

### 概念

建造者设计模式（Builder Design Pattern）又叫作生成器模式，是一种对象构建模式，其核心在于将产品和产品建造过程解耦。

建设者模式允许用户只通过指定复杂对象的类型和内容就可以一步一步地构建出一个复杂的对象，在这个过程当中，用户不需要知道内部的具体构建细节。

因此，建造者模式特别适合用于构建具有复杂内部结构的对象，或者是需要生成内部本身相互依赖的对象。

### 与工厂模式的区别

工厂模式注重的是整体对象的创建方法，而建造者模式注重的是对象的创建过程，创建对象的过程方法可以在创建时自由调用。

它们的区别还可以使用以下的经典例子解释：

> 顾客走进一家餐厅点餐，根据用户不同的选择，可以利用工厂模式来制作不同的食物，比如披萨、汉堡、沙拉。对于披萨来说，用户又有各种配料可以定制，比如奶酪、西红柿、起司，可以通过建造者模式根据用户选择的不同配料来制作披萨。
>

## 实现方式

传统的建造者模式会包括以下 4 部分：

- 建造者 Builder 抽象类：构建对象的抽象类，包括 `buildPartX（）` 相关的构建对象方法，以及 `getResult()` 这样获取实际对象的方法
- 建造者 ConcreteBuilder 具体类：继承自 Builder 抽象类，完善及构建自己特殊的 `buildPartX()` 方法
- 指挥者 Director 具体类：与客户端对接，然后根据需要创建出所需的对象，可以看做是一个代理
- 产品 Product 具体类：实际被构建出来的实际产品类

简单的 Builder 抽象类代码示例如下：

```java
public abstract class Builder {
    abstract void buildPartA();
    abstract void buildPartB();
    abstract Product createProduct();
}
```

具体的 ConcreteBuilder 类代码示例如下：

```java
public class ConcreteBuilder extends Builder {
    private Product product = new Product();

    public void buildPartA() {
        product.setPartA("A");
    }

    public void buildPartB() {
        product.setPartB("B");
    }

    public Product createProduct() {
        return product;
    }
}
```

产品 Product 类代码示例如下：

```java
public class Product {
    private String partA;
    private String partB;

    public void setPartA(String partA) {
        this.partA = partA;
    }

    public void setPartB(String partB) {
        this.partB = partB;
    }
}
```

指挥者 Director 类代码示例如下：

```java
public class Director {
    private Builder builder;

    public Director(Builder builder) {
        this.builder = builder;
    }

    public Product getProductA() {
        builder.buildPartA();
        return builder.createProduct();
    }

    public Product getProductB() {
        builder.buildPartB();
        return builder.createProduct();
    }
}
```

## 具体实现

传统的建造者模式所需的部分较多，在实际开发过程中，是需要简化系统结构，减少程序当中类的个数，并且降低用户的使用成本。

另一个常见的使用方式是，通过直接将 Builder 类作为产品类的静态内部类，这种方式更多的是降低较多成员变量时构建对象的复杂度。

以下是创建 ResourcePoolConfig 类的建造者模式代码示例：

```java
public class ResourcePoolConfig {
    private String name;
    private int maxTotal;
    private int maxIdle;
    private int minIdle;

    private ResourcePoolConfig(Builder builder) {
        this.name = builder.name;
        this.maxTotal = builder.maxTotal;
        this.maxIdle = builder.maxIdle;
        this.minIdle = builder.minIdle;
    }
    // 将 Builder 类设计成了 ResourcePoolConfig 的内部类
    // 也可以将 Builder 类设计成独立的 ResourcePoolConfigBuilder 非内部类
    public static class Builder {
        private static final int DEFAULT_MAX_TOTAL = 8;
        private static final int DEFAULT_MAX_IDLE = 8;
        private static final int DEFAULT_MIN_IDLE = 0;

        private String name;
        private int maxTotal = DEFAULT_MAX_TOTAL;
        private int maxIdle = DEFAULT_MAX_IDLE;
        private int minIdle = DEFAULT_MIN_IDLE;

        public ResourcePoolConfig build() {
            // 校验逻辑放到这里来做，包括必填项校验、依赖关系校验、约束条件校验等
            if (name == null || name.isEmpty()) {
                throw new IllegalArgumentException("...");
            }
            if (maxIdle > maxTotal) {
                throw new IllegalArgumentException("...");
            }
            if (minIdle > maxTotal || minIdle > maxIdle) {
                throw new IllegalArgumentException("...");
            }
            return new ResourcePoolConfig(this);
        }

        public Builder setName(String name) {
            if (name == null || name.isEmpty()) {
                throw new IllegalArgumentException("...");
            }
            this.name = name;
            return this;
        }
        public Builder setMaxTotal(int maxTotal) {
            if (maxTotal <= 0) {
                throw new IllegalArgumentException("...");
            }
            this.maxTotal = maxTotal;
            return this;
        }
        public Builder setMaxIdle(int maxIdle) {
            if (maxIdle < 0) {
                throw new IllegalArgumentException("...");
            }
            this.maxIdle = maxIdle;
            return this;
        }
        public Builder setMinIdle(int minIdle) {
            if (minIdle < 0) {
                throw new IllegalArgumentException("...");
            }
            this.minIdle = minIdle;
            return this;
        }
    }
}
```

以下是 ResourcePoolConfig 类建造者模式的使用方式：

```java
ResourcePoolConfig config = new ResourcePoolConfig.Builder()
    .setName("dbconnectionpool")
    .setMaxTotal(16)
    .setMaxIdle(10)
    .setMinIdle(6)
    .build();
```

上述的示例代码只是传入 4 个参数，就可以看得出构建一个对象的复杂性了，如果所需构建参数的数量更多，使用建造者模式的优势将会更加明显，传递参数更加灵活，代码具有更高的可读性。

## 总结

### 优点

建造者模式的主要优点如下：

- 使用建造者模式可以使客户端不必知道产品内部组成的细节
- 具体的建造者类之间是相互独立的，这有利于系统的扩展
- 具体的建造者相互独立，因此可以对建造的过程逐步细化，而不会对其他模块产生任何影响

### 缺点

建造者模式的主要缺点如下：

- 建造者模式所创建的产品一般具有较多的共同点，其组成部分相似，如果产品之间的差异性很大，则不适合使用建造者模式
- 如果产品的内部变化复杂，可能会导致需要定义很多具体建造者类来实现这种变化，导致系统变得很庞大，增加系统的理解难度和运行成本

### 适用场景

建造者模式的适用场景如下：

- 类的内部非常复杂，包含多个成员变量
- 类的属性之间有一定的依赖关系或者约束条件，需要指定其生成顺序
- 希望创建的对象是不可变的，在对象创建之后不能修改对象内部的属性值，暴露 setter 方法的创建对象方式就不适用了

### 源码

在 JDK 中，`java.lang.StringBuilder` 是经典的建造者模式。

其中具体的如 `append()`、`delete()`、`reverse()` 等方法都是直接返回当前 StringBuilder 对象，其可以再继续调动类似的方法以构建出一个想要的 StringBuilder 对象。

如下是使用 StringBuilder 的一个代码示例：

```java
StringBuilder stringBuilder = new StringBuilder()
        .append("olleH")
        .delete(0, 1)
        .insert(0, "o")
        .reverse();
// Hello
System.out.println(stringBuilder);
```

