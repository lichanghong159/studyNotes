# storm

![img](storm基本概念.assets/storm-flow.png)

官方的介绍：

> Apache Storm是一个分布式实时大数据处理系统。 Storm旨在以容错和水平可扩展方法处理大量数据。 它是一种流数据框架，具有最高的摄取率。 虽然Storm是无状态的，但它通过Apache ZooKeeper管理分布式环境和集群状态。 它很简单，您可以并行执行对实时数据的各种操作。

个人理解：

> * 分布式、实时大数据处理系统。流数据计算框架，高度摄取数据。
>
> * Storm是无状态的，通过ZK管理分布式环境和集群状态。
> * 可以执行各种数据的实时并行计算

Storm有许多用例：实时分析，在线机器学习，连续计算，分布式RPC(DRPC)，ETL等。storm很快：一个基准测试表示**每个节点每秒处理**超过**一百万个tuple**。它具有可扩展性，容错性，可确保您的数据得到处理，并且易于设置和操作。

Storm集成了您已经使用的队列和数据库技术。Storm拓扑消耗数据流并以任意复杂的方式处理这些流，然后在计算的每个阶段之间重新划分流。

## Apache Storm vs Hadoop

Hadoop实时计算欠佳，Storm 没有持久化处理

| Storm                                                        | Hadoop                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 实时计算                                                     | 批处理                                                       |
| 无状态                                                       | 有状态                                                       |
| 基于ZK的主从架构，主节点称为nimbus，slave是监督者。          | 基于/不基于ZooKeeper协调的主从架构。 主节点是作业跟踪器，从节点是任务跟踪器 |
| Storm流处理每秒可以处理数万消息                              | Hadoop分布式文件系统（HDFS）使用MapReduce框架处理大量数据需要几分钟或几小时 |
| 拓扑运行直到用户关闭或意外的不可恢复故障。                   | MapReduce作业按顺序执行并最终完成                            |
| 分布式和容错的                                               | 分布式和容错的                                               |
| 如果nimbus / supervisor死机，重新启动会使其从停止的位置继续运行，因此不会受到任何影响 | 如果JobTracker死亡，则所有正在运行的作业都将丢失             |



# 概念

## Topology(拓扑)

Storm 拓扑指实时应用程序的逻辑。Storm拓扑类似于MapReduce作业。一个关键的区别是MapReduce作业最终完成，而拓扑结构永远运行（当然，直到你杀死它）。拓扑是与流分组连接的spout(spouts )和bolt（bolts）的图形。这些概念如下所述。

**Resources:**

- [TopologyBuilder](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/TopologyBuilder.html)：使用此类在Java中构建拓扑

  TopologyBuilder公开了Java API，用于指定要执行的Storm拓扑。拓扑结构最终是Thrift结构，但由于Thrift API非常冗长，拓扑构建器极大地简化了创建拓扑的过程。用于创建和提交拓扑的模板类似于：

  ```java
  TopologyBuilder builder = new TopologyBuilder();
  
  builder.setSpout("1", new TestWordSpout(true), 5);
  builder.setSpout("2", new TestWordSpout(true), 3);
  builder.setBolt("3", new TestWordCounter(), 3)
           .fieldsGrouping("1", new Fields("word"))
           .fieldsGrouping("2", new Fields("word"));
  builder.setBolt("4", new TestGlobalCount())
           .globalGrouping("1");
  
  Map conf = new HashMap();
  conf.put(Config.TOPOLOGY_WORKERS, 4);
  
  StormSubmitter.submitTopology("mytopology", conf, builder.createTopology());
  ```

  在本地模式（正在处理）中运行完全相同的拓扑，并将其配置为记录所有发出的tuple，如下所示。请注意，在关闭本地群集之前，它允许拓扑运行10秒。

  ```java
  TopologyBuilder builder = new TopologyBuilder();
  
  builder.setSpout("1", new TestWordSpout(true), 5);
  builder.setSpout("2", new TestWordSpout(true), 3);
  builder.setBolt("3", new TestWordCounter(), 3)
           .fieldsGrouping("1", new Fields("word"))
           .fieldsGrouping("2", new Fields("word"));
  builder.setBolt("4", new TestGlobalCount())
           .globalGrouping("1");
  
  Map conf = new HashMap();
  conf.put(Config.TOPOLOGY_WORKERS, 4);
  conf.put(Config.TOPOLOGY_DEBUG, true);
  
  LocalCluster cluster = new LocalCluster();
  cluster.submitTopology("mytopology", conf, builder.createTopology());
  Utils.sleep(10000);
  cluster.shutdown();
  ```

- [在生产群集上运行拓扑](http://storm.apache.org/releases/1.2.2/Running-topologies-on-a-production-cluster.html)

- [本地模式](http://storm.apache.org/releases/1.2.2/Local-mode.html)：阅读本文以了解如何在本地模式下开发和测试拓扑。

## Streams

`stream `是Storm中的核心抽象。流是无限的tuple序列，以分布式方式并行处理和创建。流定义了一个模式，该模式命名流的tuple中的字段。默认情况下，tuple(`tuple`)可以包含`integer`，`long`，`short`，`byte`，`String`，`double`，`float`，`boolean`和字节数组。您还可以定义自己的序列化程序，以便可以在tuple中本机使用自定义类型。

声明时，每个流都被赋予一个id。由于单流`spouts`和`bolts `非常常见，因此[OutputFieldsDeclarer](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/OutputFieldsDeclarer.html)具有用于在不指定id的情况下声明单个流的便捷方法。在这种情况下，流的默认ID为“default”。

**资源：**

- [Tuple](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/tuple/Tuple.html)：流由tuple组成
- [OutputFieldsDeclarer](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/OutputFieldsDeclarer.html)：用于声明流及其模式
- [序列化](http://storm.apache.org/releases/1.2.2/Serialization.html)：有关Storm动态键入tuple和声明自定义序列化的信息

## Spouts(喷嘴)

spout 是拓扑中流的来源。通常，spouts将从外部源读取tuple并将它们发送到拓扑中（例如，Kestrel队列或Twitter API）。spout 可以是**可靠的**或**不可靠的**。如果一个tuple无法被Storm处理，那么一个可靠的spout能够重放一个tuple，而一个不可靠的spout一旦发出就会忘记tuple。

Spouts可以发出多个流。 为此，请使用`OutputFieldsDeclarer`的`declareStream`方法声明多个流，并在`SpoutOutputCollector`上使用`emit`方法时指定要发出的流。

spouts的主要方法是`nextTuple`。 `nextTuple`要么在拓扑中发出新tuple，要么只在没有要发出的新tuple时返回。 `nextTuple`必须阻止任何spout实现，因为Storm会在同一个线程上调用所有spout方法。

spout上的其他主要方法是`ack `和`fail`。 当Storm检测到从`spouts`发出的tuple通过拓扑成功完成或未能完成时，会调用这些元素。 `ack`和`fail`仅被称为可靠的`spouts`。 有关更多信息，请参阅Javadoc。

**资源：**

- [IRichSpout](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/IRichSpout.html): 这是[spouts](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/IRichSpout.html)必须实现的接口。
- [Guaranteeing message processing](http://storm.apache.org/releases/1.2.2/Guaranteeing-message-processing.html)(保证消息处理)

## Bolts(bolt)

拓扑中的所有处理都是用`bolts`完成的。Bolts可以执行任何操作，包括过滤，函数，聚合，连接，与数据库交谈等。

`bolts`可以进行简单的流转换。进行复杂的流转换通常需要多个步骤，因此需要多个`bolts`。例如，将推文流转换为趋势图像流至少需要两个步骤：一个`bolt`用于为每个图像执行转推滚动计数，一个或多个`bolts`用于流出顶部X图像（您可以执行此操作）使用三个`bolts `而不是两个`bolts `以更可扩展的方式进行特定的流转换。

`Bolts `可以发出多个流。 为此，请使用[`OutputFieldsDeclarer`](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/OutputFieldsDeclarer.html) 的declareStream方法声明多个流，并在 [`OutputCollector`](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/task/OutputCollector.html)上使用emit方法时指定要发出的流。

声明bolt的输入流时，总是订阅另一个组件的特定流。如果要订阅另一个组件的所有流，则必须单独订阅每个组件。[InputDeclarer](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/InputDeclarer.html)具有语法糖，用于订阅在默认流ID上声明的流。说`declarer.shuffleGrouping("1")`订阅组件“1”上的默认流并且相当于`declarer.shuffleGrouping("1", DEFAULT_STREAM_ID)`。

Bolt中的主要方法是`execute`接收新tuple作为输入的方法。Bolts使用[OutputCollector](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/task/OutputCollector.html)对象发出[新元](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/task/OutputCollector.html)组。bolt必须`ack`在`OutputCollector`它们处理的每个tuple上调用方法，以便Storm知道tuple何时完成（并且最终可以确定它可以安全地确定原始的spouttuple）。对于处理输入tuple，基于该tuple发出0或更多tuple，然后对输入tuple进行调整的常见情况，Storm提供了一个[IBasicBolt](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/IBasicBolt.html)接口，它自动执行[acking](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/IBasicBolt.html)。

在异步处理的bolt中启动新线程是完全没问题的。[OutputCollector](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/task/OutputCollector.html)是线程安全的，可以随时调用。

**资源：**

- [IRichBolt](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/IRichBolt.html)：这是bolt的通用接口。
- [IBasicBolt](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/IBasicBolt.html)：这是一个方便的界面，用于定义执行过滤或简单功能的bolt。
- [OutputCollector](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/task/OutputCollector.html)：[bolt](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/task/OutputCollector.html)使用此类的实例将tuple发送到其输出流
- [保证消息处理](http://storm.apache.org/releases/1.2.2/Guaranteeing-message-processing.html)

## Stream groupings(流分组)

定义拓扑的一部分是为每个bolt指定它应该作为输入接收的流。流分组定义了如何在bolt的任务中对该流进行分区。

Storm中有八个内置流分组，您可以通过实现[CustomStreamGrouping](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/grouping/CustomStreamGrouping.html)接口来实现自定义流分组：

1. **Shuffle grouping(随机分组)**：tuple随机分布在bolt的任务中，使得每个bolt都能保证获得相同数量的tuple。
2. **Fields grouping(字段分组)**：流按分组中指定的字段进行分区。例如，如果流按“user-id”字段分组，则具有相同“user-id”的tuple将始终执行相同的任务，但具有不同“user-id”的tuple可能会执行不同的任务。
3. **Partial Key grouping(部分密钥分组)**：流按分组中指定的字段进行分区，如字段分组，但在两个下游bolt之间进行负载平衡，这可在传入数据偏斜时提供更好的资源利用率。[本文](https://melmeric.files.wordpress.com/2014/11/the-power-of-both-choices-practical-load-balancing-for-distributed-stream-processing-engines.pdf)对其工作原理及其提供的优势进行了很好的解释。
4. **All grouping(所有分组)**：流被复制到所有bolt任务中。小心使用此分组。
5. **Global grouping(全局分组)**：整个流转到了一个bolt的任务。具体来说，它转到id最低的任务。
6. **None grouping(无分组)**：此分组指定您不关心流的分组方式。目前，没有任何分组相当于随机分组。最终，Storm会按下没有分组的bolt，在与他们订购的bolt或spout相同的螺纹中执行（如果可能的话）。
7. **Direct grouping(直接分组)**：这是一种特殊的分组。以这种方式分组的流意味着tuple的**生产者**决定消费者的哪个任务将接收该tuple。直接分组只能在已声明为直接流的流上声明。发送到直接流的tuple必须使用[emitDirect]之一(javadocs/org/apache/storm/task/OutputCollector.html#emitDirect(int, int, java.util.List)方法发出。一个bolt可以得到通过使用提供的[TopologyContext](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/task/TopologyContext.html)或跟踪[OutputCollector](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/task/OutputCollector.html)中的`emit`方法输出（返回tuple发送到的任务ID）来消费者的任务ID。
8. **Local or shuffle grouping(本地或随机分组)**：如果目标bolt在同一工作进程中有一个或多个任务，则tuple将被洗牌到只有那些进程内任务。否则，这就像一个普通的shuffle分组。

**资源：**

- [TopologyBuilder](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/TopologyBuilder.html)：使用此类来定义拓扑
- [InputDeclarer](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/InputDeclarer.html)：`setBolt`调用on 时返回此对象`TopologyBuilder`，用于声明bolt的输入流以及如何对这些流进行分组

## 可靠性

Storm保证每个spout tuple都将由拓扑完全处理。它通过跟踪每个spout tuple触发的tuple树并确定该tuple树何时成功完成来实现此目的。每个拓扑都有一个与其关联的“消息超时”。如果Storm未能检测到在该超时内已完成一个spout tuple，则它会使tuple失败并在以后重播。

要利用Storm的可靠性功能，必须在创建tuple树中的新边时告诉Storm，并在完成处理单个tuple时告诉Storm。这些是使用[OutputCollector](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/task/OutputCollector.html)对象完成的，该对象用于发出tuple。锚定在`emit`方法中完成，并声明您已使用该`ack`方法完成tuple。

在[保证消息处理中](http://storm.apache.org/releases/1.2.2/Guaranteeing-message-processing.html)更详细地解释了这一点。

## Tasks

每个spout或bolt在整个集群中执行任意数量的任务。每个任务对应一个执行线程，流分组定义如何将tuple从一组任务发送到另一组任务。您可以在[TopologyBuilder](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/topology/TopologyBuilder.html)的`setSpout`and和`setBolt`方法中为每个spout或bolt设置并行度。

## Workers

拓扑在一个或多个工作进程中执行。每个工作进程都是物理JVM，并执行拓扑的所有任务的子集。例如，如果拓扑的组合并行度为300且分配了50个工作线程，则每个工作线程将执行6个任务（作为工作线程中的线程）。Storm试图在所有工作人员之间平均分配任务。

# 保证消息处理

Storm提供了几种不同级别的保证消息处理，包括尽力而为，至少一次，以及通过[Trident](http://storm.apache.org/releases/1.2.2/Trident-tutorial.html)完成一次。此页面描述了Storm如何保证至少一次处理。

## 消息“完全处理”是什么意思？

从`spout `喷出的tuple可以触发数千个基于它的tuple。例如，考虑流式字数统计拓扑：

```java
TopologyBuilder builder = new TopologyBuilder();
builder.setSpout("sentences", new KestrelSpout("kestrel.backtype.com",
                                               22133,
                                               "sentence_queue",
                                               new StringScheme()));
builder.setBolt("split", new SplitSentence(), 10)
        .shuffleGrouping("sentences");
builder.setBolt("count", new WordCount(), 20)
        .fieldsGrouping("split", new Fields("word"));
```

这种拓扑结构从Kestrel队列中读出句子，将句子分成其组成单词，然后为每个单词发出之前看过该单词的次数。 从spout喷出的tuple触发了许多基于它的tuple：句子中每个单词的tuple和每个单词更新计数的tuple。 消息树看起来像这样：

![Tuple tree](storm基本概念.assets/tuple_tree.png)

当tuple树已经用完并且树中的每条消息都已处理完毕时，Storm认为一个tuple从一个“完全处理”的spout中出来。 如果在指定的超时内无法完全处理其消息树，则认为该tuple失败。 可以使用Config.TOPOLOGY_MESSAGE_TIMEOUT_SECS配置在特定于拓扑的基础上配置此超时，默认为30秒。

## 如果信息已完全处理或未能完全处理，会发生什么？

要理解这个问题，让我们来看看`spout`出来的tuple的生命周期。作为参考，这是spouts实现的接口（有关更多信息，请参阅[Javadoc](http://storm.apache.org/releases/1.2.2/javadocs/org/apache/storm/spout/ISpout.html)）：

```java
public interface ISpout extends Serializable {
    void open(Map conf, TopologyContext context, SpoutOutputCollector collector);
    void close();
    void nextTuple();
    void ack(Object msgId);
    void fail(Object msgId);
}
```

