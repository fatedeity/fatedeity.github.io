
创建对象可能会导致大量的重复代码，可能会需要复合对象访问不到的信息，也可能提供不了足够级别的抽象，还可能并不是复合对象概念的一部分。工厂设计模式通过定义一个单独的创建对象的方法来解决这些问题。

<!--more-->

## 简介

工厂设计模式（Factory Design Pattern）是一种创建型的设计模式，它提供了一种创建对象的最佳方式，是一种代替 `new` 操作符的一种模式。

在工厂模式中，创建对象不会对客户端暴露创建逻辑，而是通过使用一个共同的接口来指向新创建的对象。

工厂模式还可以细分为三种的类型：简单工厂模式、工厂方法模式和抽象工厂模式。

## 简单工厂模式

### 概念

简单工厂模式（Simple Factory Pattern）的定义是，由一个工厂对象决定创建出哪一种产品类的实例，被创建的产品类实例具有共同的父类或实现同样的接口。

因为在简单工厂模式中用于创建实例的方法通常是静态方法，因此简单工厂模式又被称为静态工厂模式（Static Factory Pattern）。

### 实现方式

下面是使用果汁生产来展示简单工厂模式：

果汁 FruitJuice 接口：描述生产果汁的必要方法

```java
public interface FruitJuice {
    void make();
}
```

苹果汁 AppleJuice 类：生产苹果汁的类

```java
public class AppleJuice implements FruitJuice {
    public AppleJuice() {
        this.make();
    }

    @Override
    public void make() {
        System.out.println("This is AppleJuice make!");
    }
}
```

橙汁 OrangeJuice 类：生产橙汁的类

```java
public class OrangeJuice implements FruitJuice {
    public OrangeJuice() {
        this.make();
    }

    @Override
    public void make() {
        System.out.println("This is OrangeJuice make!");
    }
}
```

果汁工厂 FruitJuiceFactory 类：负责生产各种果汁的类

```java
public class FruitJuiceFactory {
    public FruitJuiceFactory() {}

    public static FruitJuice createFruitJuice(String juiceType) {
        FruitJuice fruitJuice = null;
        if (juiceType.equals("AppleJuice")) {
            fruitJuice = new AppleJuice();
        } else if (juiceType.equals("OrangeJuice")) {
            fruitJuice = new OrangeJuice();
        }
        return fruitJuice;
    }
}
```

通过 if-else 逻辑是简单工厂的第一种实现方法，还可以通过静态代码块和 Map 结构实现简单工厂模式，具体的代码示例如下：

```java
import java.util.Map;
import java.util.HashMap;

public class FruitJuiceFactory {
    // 存储产品类的映射关系
    private static final Map<String, FruitJuice> cachedFruitJuice = new HashMap<>();

    static {
        cachedFruitJuice.put("AppleJuice", new AppleJuice());
        cachedFruitJuice.put("OrangeJuice", new OrangeJuice());
    }

    public FruitJuiceFactory() {}

    public static FruitJuice createFruitJuice(String juiceType) {
        if (juiceType == null || juiceType.isEmpty()) {
            return null;
        }
        // 直接从 Map 结构中取到对应的产品类实例
        return cachedFruitJuice.get(juiceType.toLowerCase());
    }
}
```

### 优点

简单工厂模式的主要优点如下：

* 简单工厂模式实现了对象创建和使用的分离
* 客户端无需知道所创建的具体产品类的类名，只需要知道具体产品类所对应的参数即可
* 可以在不改变客户端代码的情况下更换或增加新的具体产品类

### 缺点

简单工厂模式的主要缺点如下：

* 工厂类集中了相似产品类的创建逻辑，职责过重
* 简单工厂模式违反开闭原则，新增产品类时需要改动到工厂类
* 通常使用静态方法作为创建实例的方法，无法实现继承关系

### 适用场景

简单工厂模式的适用场景如下：

* 对于一批产品类，并且不会新增产品类，可以选择简单工厂模式

### 源码

在 JDK 中，`java.util.Calendar` 使用了简单工厂模式。如下是其的一些实现逻辑：

```java
public abstract class Calendar implements Serializable, Cloneable, Comparable<Calendar> {
    private static Calendar createCalendar(TimeZone zone, Locale aLocale) {
        CalendarProvider provider =
            LocaleProviderAdapter.getAdapter(CalendarProvider.class, aLocale)
                                 .getCalendarProvider();
        if (provider != null) {
            try {
                return provider.getInstance(zone, aLocale);
            } catch (IllegalArgumentException iae) {
                // fall back to the default instantiation
            }
        }

        Calendar cal = null;

        if (aLocale.hasExtensions()) {
            String caltype = aLocale.getUnicodeLocaleType("ca");
            if (caltype != null) {
                cal = switch (caltype) {
                    case "buddhist" -> new BuddhistCalendar(zone, aLocale);
                    case "japanese" -> new JapaneseImperialCalendar(zone, aLocale);
                    case "gregory"  -> new GregorianCalendar(zone, aLocale);
                    default         -> null;
                };
            }
        }
        if (cal == null) {
            if (aLocale.getLanguage() == "th" && aLocale.getCountry() == "TH") {
                cal = new BuddhistCalendar(zone, aLocale);
            } else if (aLocale.getVariant() == "JP" && aLocale.getLanguage() == "ja"
                       && aLocale.getCountry() == "JP") {
                cal = new JapaneseImperialCalendar(zone, aLocale);
            } else {
                cal = new GregorianCalendar(zone, aLocale);
            }
        }
        return cal;
    }
}
```

## 工厂方法模式

### 概念

简单工厂模式违背了开闭原则，没有能够做到“对扩展开放、对修改关闭”，新增产品类时需要改动到工厂类。

而工厂方法模式（Factory Method Pattern）是对简单工厂模式的进一步抽象，其好处是可以使系统在不修改原来代码的情况下引入新的产品类，即满足开闭原则。

和简单工厂模式中工厂负责生产所有的产品相比，工厂方法模式抽象出简单工厂的接口，然后将生产具体产品的任务分发给具体的产品工厂，即简单工厂的工厂。

由于工厂方法模式利用了面向对象的多态特性，因此又被称为多态工厂模式（Polymorphic Factory Pattern）。

### 实现方式

在上述简单工厂模式的基础上，将果汁生产改造成工厂方法模式：

抽象工厂 AbstractFactory 接口：描述生产果汁工厂的必要方法

```java
public interface FruitJuiceFactory {
    FruitJuice createFruitJuice();
}
```

苹果汁工厂 AppleJuiceFactory 类：详细的生产苹果汁的类

```java
public class AppleJuiceFactory implements FruitJuiceFactory {
    public AppleJuiceFactory() {}

    @Override
    public FruitJuice createFruitJuice() {
        return new AppleJuice();
    }
}
```

橙汁工厂 OrangeJuiceFactory 类：详细的生产橙汁的类

```java
public class OrangeJuiceFactory implements FruitJuiceFactory {
    public OrangeJuiceFactory() {}

    @Override
    public FruitJuice createFruitJuice() {
        return new OrangeJuice();
    }
}
```

### 优点

工厂方法模式除了具有简单工厂模式的优点之外，还有以下优点：

* 在系统中增加新的产品类时，无需修改原有的工厂代码，只需添加一个具体产品类和具体工厂类

### 缺点

工厂方法模式的主要缺点如下：

* 添加新的产品类时，需要同时提供具体产品类和对应的工厂类，系统中类的个数将成对增加
* 工厂方法模式引入了抽象层，增加了系统的抽象性和理解难度

### 适用场景

工厂方法模式的适用场景如下：

* 客户端知道其需要的接口实现类，不知道所需要的具体对象类，可以通过对应的工厂创建实例

### 源码

在 JDK 中，`java.util.Collection` 接口中定义的 `iterator()` 方法就是一个工厂方法。

所有实现了 `Collection` 接口的类，都需要显式地实现此方法并返回一个 `Iterator` 实例，不同的实现类可以拥有自己的实现逻辑。

## 抽象工厂模式

### 概念

在工厂方法模式中，具体工厂负责生产具体的产品，每个具体工厂对应一种具体产品，工厂方法具有唯一性。

抽象工厂模式（Abstract Factory Pattern）定义了一个接口用于创建相关或有依赖关系的对象族，而无需指明具体的类，其可以整合简单工厂模式和抽象工厂模式。

学习抽象工厂模式需要理解两个概念：产品等级结构指的是产品的继承结构，如抽象果汁和具体果汁之间构成一个产品等级结构；产品族指的是由同一个工厂生产的，位于不同等级结构中的一组产品，如同一个饮料工厂生产的酒和果汁构成一个产品族。

从上述概念上理解，工厂方法模式针对的是一个产品等级结构，而抽象工厂模式在工厂方法模式的基础上，覆盖了多个工厂的产品族。

### 实现方式

在上述简单工厂模式的基础上，下面使用果汁生产和酒生产来展示抽象工厂模式：

酒 Alcohol 接口：描述生产酒的必要方法

```java
public interface Alcohol {
    void make();
}
```

葡萄酒 Wine 类：详细的生产葡萄酒的类

```java
public class Wine implements Alcohol {
    public Wine() {
        this.make();
    }

    @Override
    public void make() {
        System.out.println("This is Wine make!");
    }
}
```

啤酒 Beer 类：详细的生产啤酒的类

```java
public class Beer implements Alcohol {
    public Beer() {
        this.make();
    }

    @Override
    public void make() {
        System.out.println("This is Beer make!");
    }
}
```

抽象工厂 AbstractFactory 接口：描述生产果汁和酒的必要方法

```java
public interface AbstractFactory {
    FruitJuice creatFruitJuice();
    Alcohol createAlcohol();
}
```

具体工厂 ConcreteFactory 类：可生产果汁和酒的工厂的类

```java
public class ConcreteFactory implements AbstractFactory {
    public ConcreteFactory() {}

    @Override
    public FruitJuice creatFruitJuice(String juiceType) {
        FruitJuice fruitJuice = null;
        if (juiceType.equals("AppleJuice")) {
            fruitJuice = new AppleJuice();
        } else if (juiceType.equals("OrangeJuice")) {
            fruitJuice = new OrangeJuice();
        }
        return fruitJuice;
    }

    @Override
    public Alcohol createAlcohol(String alcoholType) {
        Alcohol alcohol = null;
        if (alcoholType.equals("Wine")) {
            alcohol = new Wine();
        } else if (alcoholType.equals("Beer")) {
            alcohol = new Beer();
        }
        return alcohol;
    }
}
```

### 优点

抽象工厂模式的主要优点如下：

* 当一个产品族中的多个对象被设计成一起工作时，它能够保证客户端始终只使用同一个产品族中的对象
* 增加新的产品族很方便，无需修改已有代码，符合开闭原则

### 缺点

抽象工厂模式的主要缺点如下：

* 增加新的产品等级结构麻烦，需要对原有系统进行较大的修改，甚至需要修改抽象层代码

### 适用场景

抽象工厂模式的适用场景如下：

* 当需要创建的对象是一系列相互关联或相互依赖的产品族时，便可以使用抽象工厂模式
* 产品等级结构稳定，设计完成之后，不会向系统中增加新的产品等级结构或者删除已有的产品等级结构

### 源码

在 JDK 中，`java.util.Collection` 接口可以看作一个抽象工厂。

`Collection` 接口定义了转换成 `Iterator` 实例的工厂方法，也定义了转换成 `T[]` 实例的工厂方法，可以认定其做了两个产品族的定义。

## 总结

### 使用标准

对于要不要使用工厂模式，其最本质的参考标准有以下四个：

* 封装变化：创建逻辑有可能变化，封装成工厂类之后，创建逻辑的变更对调用者透明
* 代码复用：创建代码抽离到独立的工厂类之后可以复用
* 隔离复杂性：封装复杂的创建逻辑，调用者无需了解如何创建对象
* 控制复杂度：将创建代码抽离出来，让原本的函数或类职责更单一，代码更简洁

### 应用场景

对于产品种类相对较少、且可预见性地不会修改的情况，可以使用简单工厂模式。使用简单工厂模式的客户端只需传入工厂类的参数，不需要关心如何创建对象的逻辑，可以很方便地创建所需产品。

由于简单工厂模式会将所有创建逻辑都放在一个工厂类中，会导致这个工厂类会变得很复杂，当产品的种类是可预见地会增加时，还需要对工厂类做更改，这种时候可以采用工厂方法模式以达到不需要修改原来代码的情况下引进新的产品。

抽象工厂模式最早的应用是用于创建属于不同操作系统的视窗构件。当需要创建的对象是一系列相互关联或相互依赖的产品族时，或者是只会新增新的产品族而不是新种类的产品时，可以使用抽象工厂模式。

