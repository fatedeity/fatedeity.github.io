# MongoDB - 数据模型的设计模式


在实际开发中，大多数性能问题都可以追溯到糟糕的模型设计。官方也提供分享过文档模型设计的进阶技巧，这里简单翻译记录一下。

<!--more-->

## 简介

官方文章的地址是 [Building with Patterns: A Summary](https://www.mongodb.com/blog/post/building-with-patterns-a-summary)，其中汇总了 12 种设计模式及使用场景。

![设计模式汇总表](assets/设计模式汇总表.png)

上述的图表列举了 12 种设计模式及应用场景，主要是以下这些：

- 近似值模式（Approximation Pattern）
- 属性模式（Attribute Pattern）
- 桶模式（Bucket Pattern）
- 计算模式（Computed Pattern）
- 文档版本控制模式（Document Versioning Pattern）
- 扩展引用模式（Extended Peference Pattern）
- 异常值模式（Outlier Pattern）
- 预分配模式（Preallocated Pattern）
- 多态模式（Polymorphic Pattern）
- 模式版本控制模式（Schema Versioning Pattern）
- 子集模式（Subset Pattern）
- 树型模式（Tree and Graph Pattern）

## 近似值模式

### 示例描述

淘宝在往年“双十一”都会有一个销售额大屏展示，当销售额小于 1 亿时，可能展示的是实际的数量，当销售额超过 1 亿时，单位立即变成以“亿”为单位，对于展示的大屏而言，”亿“以下的单位这个时候并不是很重要了。

对于上述的场景，如果每次几十、几百都直接去更新数据库中的实际值，则更新数据库会变得非常频繁，对于数据库的压力是非常大的。

实际上，并不需要每次都去更新数据库，我们只需要将这个实际的精确值存储在内存中，使用 1 亿作为一个阈值，一旦超过这个阈值就精确值更新进数据库中。

对于精度不是首要考虑因素时，那么就可以使用近似值模式，尤其是消耗资源（时间、内存、CPU 周期）非常昂贵时效果会更佳。

近似值模式就是通过减少数据的写入频率，从而降低了架构的复杂度和资源开销，进而提升了整体的性能与效率。

### 优缺点

近似值模式的优点如下：

- 由于是近似的数据，不必时时刻刻写入，数据库的写操作量级更小

近似值模式的缺点如下：

- 存储的是近似的数据，无法应对需要展示精确数据的场景
- 此模式需要在应用层实现

## 属性模式

### 示例描述

属性模式运用到了 MongoDB 多键索引的概念，支持对数组中的嵌套子文档中的某个属性进行索引。

假设现在有一个关于电影的集合，其中文档中会包含标题、导演、制片人、演员、上映时间等等信息，对于跨地区上映的电影，有可能不同地区的上映时间是不一样的。

如下展示是一条电影的文档数据：

```javascript
{
    "title": "Star Wars",
    "director": "George Lucas",
    // 不同地区有不同的上映时间
    "release_US": ISODate("1977-05-20T01:00:00+01:00"),
    "release_France": ISODate("1977-10-19T01:00:00+01:00"),
    "release_Italy": ISODate("1977-10-20T01:00:00+01:00"),
    "release_UK": ISODate("1977-12-27T01:00:00+01:00"),
}
```

为了支持对所有上映时间做一个快速搜索，也许我们需要将所有的上映时间设置为单一索引，这个时候，索引的数量就会变得显而易见的多。

使用属性模式，我们通过将这些上映时间信息移动到一个数组中，然后再对这个数组建立一个多键索引索引，以实现使用一个索引替代多个类似索引的功能。

如下是修改结构后的文档数据：

```javascript
{
    "title": "Star Wars",
    "director": "George Lucas",
    // 所有的地区的上映时间都放在同一个属性内部
    "releaseList": [
        {
            "region": "US",
            "date": ISODate("1977-05-20T01:00:00+01:00")
        },
        {
            "region": "France",
            "date": ISODate("1977-10-19T01:00:00+01:00")
        },
        {
            "region": "Italy",
            "date": ISODate("1977-10-20T01:00:00+01:00")
        },
        {
            "region": "UK",
            "date": ISODate("1977-12-27T01:00:00+01:00")
        }
    ]
}
```

### 优缺点

属性模式的优点如下：

- 需要更少的索引
- 查询变得更容易编写，而且通常更快

## 桶模式

### 示例描述

桶模式有点类似于水平分库，常见的水平分库是将一个集合按照某一个规则分布到不同的数据库上，桶模式是将一个集合中的文档按照某一个规则合并起来。

假设现在有一个需要记录用户日志的需求，对于用户的每一个动作，都需要将其更新到 MongoDB 当中，并且是记录其动作、时间。

对于这样的日志数据来说，如果将每一个动作都存储成一个文档，则将会占用极大的存储空间和内存。

使用桶模式的解决办法就是，将一段时间的日志数据存储成一个文档，再将每一个动作日志的数据存储到子文档数据中。

当需要管理流式数据的时候，如时间序列、实时分析或物联网应用程序，桶模式就是一个很好的解决方案。

### 优缺点

桶模式的优点如下：

- 减少了集合中的文档总数
- 提高了索引性能
- 可以通过预聚合简化数据的访问

## 计算模式

### 示例描述

对于大型数据集，每一次计算都可能会占用极大的 CPU、磁盘、内存等相关资源，甚至是影响到服务器上的其他计算。

而对于需要重复计算、读取比写入多的场景，计算模式提供了一种优化的思路，以便降低服务器资源的占用。

拿一个电影观看总人数的例子来说明：假设现在页面上需要展示观看电影的实际总人数，而且这个页面会有成千上万的人访问。

虽然，我们可以对电影的每一次放映都记录起观看人数，但是要获取总人数，则需要拿出所有的放映场次的观看人数之后计算其总和，这个计算就非常耗费资源和时间。

对于只有放映场次变化之后，总人数才会更新的情况，实际数据库读取的次数远远大于写入的次数。

![MongoDB 的计算模式](assets/MongoDB的计算模式.png)

在这个场景中，计算模式的思路是：每一次更新放映场次数据的时候，将这个放映场次的人数汇总到一个文档当中，这个文档直接面向用户的查询。

### 优缺点

计算模式的优点如下：

- 对于频繁的计算可以减少 CPU 的负载
- 查询变得更容易编写，而且通常更快

计算模式的缺点如下：

- 识别出需要使用此模式的的场景可能比较困难
- 除非必要，请勿过度使用此模式

## 文档版本控制模式

### 示例描述

文档版本控制模式在高度规范化的行业中非常有用，这些行业会要求数据的特定时间点版本。

假设现在有一个博客系统，其中有一个记录每次编辑博客文章历史的功能，这样的功能就能应用文档版本控制模式。

假设我们将所有的文章历史都存储在同一个集合当中，则需要考虑大部分与文章相关的功能都要过滤掉历史版本、版本越多则集合文档数量越多等等问题。

文档版本控制模式的想法是：文档中需要记录一个文档的版本，将最新的文档保存在一个 current 集合中，而那些旧版本的文档保存在 history 集合中。

为了最大化利用文档版本控制模式的优势，通常会假设数据访问模式尽量符合以下要求：

- 每个文档不会有太多的修订版本
- 需要做版本控制的文档不会太多
- 大多数的查询都是基于文档的最新版本

### 优缺点

文档版本控制模式的优点如下：

- 容易实现，对现有系统的影响小
- 在最新版本上进行请求时，没有性能上的影响

文档版本控制模式的缺点如下：

- 写操作的数量会翻倍
- 请求需要被定位到正确的集合

## 扩展引用模式

### 示例描述

MongoDB 是一个不需要提前建模的 NoSQL，当不同文档、不同集合之间存在关系的时候，通常会有嵌入和引用两种方式。

嵌入就是将文档数据嵌入到引用此数据的文档中，访问时直接访问这一次文档即可；引用就是只在文档中引用另一个文档的标识，访问时需要访问两次数据库才能拿到完整的数据。

扩展引用模式是指仅复制经常访问并且不经常更改的字段，而不是复制所有的数据，减少信息的连接以提高性能。

![扩展引用模式引用数据](assets/扩展引用模式引用数据.png)

这张图的场景是：客户和订单是 1 对 N 的关系，通常查询订单列表的时候需要展示客户的一些信息，我们就需要考虑是否将客户的信息冗余进订单信息中。

扩展引用模式认为，客户的名称和地址是不常做更新的，可以直接将这些信息冗余进订单表中，以达到减少两个集合连接查询的要求。

### 优缺点

扩展引用模式的优点如下：

- 当有大量的 JOIN 操作时可以提升性能
- 读操作会更快，并且可以减少 JOIN 操作的数量

扩展引用模式的缺点如下：

- 修改冗余的这部分数据会比较复杂

## 异常值模式

### 示例描述

顾名思义，异常值模式主要用以解决超出应用程序正常模式的少数异常查询情况。

假设你正在搭建一个出售图书的电子商务网站，现在需要记录一本书都有哪些用户购买过，一个常见的做法的是将购买的用户标识存储在图书文档中，如下展示：

```javascript
{
    "_id": ObjectId('6392cecd4dd9624424ad025d'),
    "title": "三国演义",
    "author": "罗贯中",
    "purchase_customers": [
        "user0",
        "user1",
        "user3",
        // ...
    ]
}
```

对于上述的文档结构，大部分情况下是适用的。但是，对于销量特别高的图书，极可能导致图书文档的大小超过 16MB 的限制。

使用异常值模式的方式是：在图书文档中添加一个字段来将其标记为异常值，超过一定大小的内容可以存储在另一个文档当中，在应用程序中对异常文档做扩展查询处理，减少异常文档对正常文档的影响。

### 优缺点

异常值模式的优点如下：

- 防止整个应用被某些异常的文档或请求所影响
- 请求会针对那些典型的用例进行优化，而异常值仍将得到处理

异常值模式的缺点如下：

- 通常会为特定的查询而进行定制，因此一些临时产生的查询可能性能不太理想
- 此模式的大部分工作是在应用程序代码中完成的

## 预分配模式

### 示例描述

在使用 MMAPv1 存储引擎时，MongoDB 的一个常见优化是提前分配所需的内存，以满足不断增长的文档未来会达到的大小。

MMAPv1 中不断增长的文档需要由服务端以相当昂贵的成本进行位置的迁移，而 WiredTiger 的无锁机制（lock-free）和重写（rewrite）更新算法不需要这种处理。

一个相对应的例子就是，直接存储一个二维数据可以做到预分配内存，而存储二维数组转换后的稀疏数组则无法做到预分配内存。

因此，在 MMAPv1 中，更推荐使用预分配模式直接存储原始的二维数组。

### 优缺点

预分配模式的优点如下：

- 当预先知道文档结构时，可以简化设计

预分配模式的缺点如下：

- 简单和性能之间的权衡

## 多态模式

### 示例描述

在面向对象中，多态指的是为不同数据类型的实体提供统一的接口，或使用一个单一的符号来表示多个不同的类型。

而 MongoDB 不强制要求集合的文档拥有特定的结构，这里的多态模式指的是，集合中的文档具有更多的相似性而不是差异性，文档结构都类似但又不完全相同。

其一种实现方案是将文档分组在一起做查询，而不是将其分散到多个集合中；另一种实现方案是使用嵌入式子文档的模式汇总。

多态模式的一个典型用例是单一视图应用程序：假设现在一家较大的公司收购了其他公司，这些公司的业务都是类似的，数据库都以类似的方式存储了数据。

这个时候就可以利用 MongoDB 和多态模式在短时间内构建好单一视图应用程序。

除了单一视图应用程序外，多态模式的其他典型用例还有以下几种：

- 内容管理
- 移动应用程序
- 产品目录

### 优缺点

多态模式的优点如下：

- 实现简单
- 查询可以在单个集合中运行

## 模式版本控制模式

### 示例描述

几乎每个数据库在其生命周期中的某个时刻都会产生变更，一旦数据库中的数据模型发生变化，通常需要停止应用程序，迁移数据库以支持新模式，然后重新启动。

这种停机更新会导致糟糕的用户体验，而模式版本控制模式允许历史版本和当前版本的文档在集合中同时存在，以此保障用户体验。

通过使用 `schema_version` 字段定义模式的版本，并将其保存到数据库中，每个新的模式版本都会增加 `schema_version` 字段的值。

在应用程序内部，为每个模式版本创建相应的处理函数，这样即可适应不同版本的数据。

### 优缺点

模式版本控制模式的优点如下：

- 不需要停机时间
- 模式迁移可控
- 减少未来的技术债务

模式版本控制模式的缺点如下：

- 在迁移过程中，对相同的字段可能需要两个索引

## 子集模式

### 示例描述

MongoDB 将频繁访问的数据保存在 RAM 中，当数据和索引的工作集超过分配的物理 RAM 时，随着磁盘访问的发生以及数据从 RAM 中转出，性能会开始下降。

为解决这个问题，一个方案是向服务器添加更多的 RAM，不过扩展会有上限，而且非常昂贵；或者考虑对集合进行分片，但这会带来额外的成本和复杂性。

子集模式就解决了有大量数据的大文档没有被应用程序使用而导致的工作集超过 RAM 容量的问题，比如说一个电商产品的评论可能有成千上万条，但大部分情况下都只会访问最近 10 个评论。

其实现方法就是，将一个存储大文档的集合拆分成多个子集，每一个子集都能为单独的功能提供资源，减少了工作集的总体大小。

### 优缺点

子集模式的优点如下：

- 在总体上减小了工作集的大小
- 缩短了最常用数据的磁盘访问时间

子集模式的缺点如下：

- 必须管理子集
- 请求附加的数据需要额外的数据库访问

## 树形模式

### 示例描述

对于 SQL 来说，可以通过外链子结点或父结点的方式表示树形结构。

对于 MongoDB 而言，比较方便的就是通过存储子结点数组的方式实现，但是其缺点就是每次更新时都需要操作整个结构，不合适做频繁更新，比如说家谱。

因此，当数据是分层结构并且经常被查询时，树形模式是比较优的一个选择。并且可以通过给数组中的属性创建多键索引提高查询效率。

### 优缺点

树形模式的优点如下：

- 通过避免多次 JOIN 操作提高了性能

树形模式的缺点如下：

- 需要在应用程序中管理树结构的更新
