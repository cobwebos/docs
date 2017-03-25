---
title: "Apache Storm on HDInsight 简介 | Microsoft 文档"
description: "获取 Apache Storm on HDInsight 简介。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 0cf2d7f4cbbed730d690693fd006665355155c22
ms.lasthandoff: 03/21/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Apache Storm on HDInsight 简介：面向 Hadoop 的实时分析

Apache Storm on HDInsight 可让你在 Azure 上创建分布式实时分析解决方案。

Apache Storm 是分布式可容错的开源计算系统，可用于配合 Hadoop 实时处理数据。 Storm 解决方案还提供有保障的数据处理功能，能够重播第一次未成功处理的数据。

## <a name="why-use-storm-on-hdinsight"></a>为何使用 Storm on HDInsight

Apache Storm on HDInsight 是已集成到 Azure 环境中的托管群集。 HDInsight 上的 Storm 和其他 Hadoop 组件基于 Hortonworks 数据平台 (HDP)，群集的操作系统为 Ubuntu（一种 Linux 分发版）。 此配置提供的平台与 Hadoop 生态系统中的热门工具与服务兼容。

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

Apache Storm on HDInsight 提供以下重要优势：

* 以托管服务的形式执行，提供 99.9% 运行时间 SLA。

* 在群集创建期间或者创建之后，通过针对群集运行脚本轻松进行自定义。 有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

* 使用所选语言：Storm 组件可以使用多种语言编写，例如 **Java**、**C#**、**Python**。
  
  * Visual Studio 与 HDInsight 集成，可以开发、管理和监视 C# 拓扑。 有关详细信息，请参阅 [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)（使用用于 Visual Studio 的 HDInsight 工具开发 C# Storm 拓扑）。

  * 支持 **Trident** Java 接口。 使用此接口可以创建支持“一次性”消息处理、“事务性”数据存储持久性和一组常见流分析操作的 Storm 拓扑。

* 轻松扩展和缩减群集：在不影响 Storm 拓扑运行的情况下添加或删除辅助角色节点。

* 与以下 Azure 服务集成：
  
    * 事件中心
    * 虚拟网络
    * SQL 数据库
    * Azure 存储空间
    * DocumentDB。
  
  * 通过使用 Azure 虚拟网络中安全组合多个 HDInsight 群集的功能：创建使用 HDInsight、HBase 或 Hadoop 群集的分析管道。

有关在实时分析解决方案中使用 Apache Storm 的公司列表，请参阅 [使用 Apache Storm 的公司](https://storm.apache.org/documentation/Powered-By.html)。

若要开始使用 Storm，请参阅 [Storm on HDInsight 入门][gettingstarted]。

### <a name="ease-of-creation"></a>容易创建

你可以在分钟数设置好新的 Storm on HDInsight 群集。 指定群集名称、大小、管理员帐户和存储帐户。 Azure 创建该群集，包括示例拓扑和 Web 管理仪表板。

> [!NOTE]
> 也可以使用 [Azure CLI](../cli-install-nodejs.md) 或 [Azure PowerShell](/powershell/azureps-cmdlets-docs) 预配 Storm 群集。

在提交请求后的 15 分钟内，你就可以运行新的 Storm 群集，并准备好建立第一个实时分析管道。

### <a name="ease-of-use"></a>易于使用

* __安全外壳连接__：可以使用 SSH 通过 Internet 访问 HDInsight 群集的头节点。 SSH 用于直接在群集上运行命令。

  有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

* __Web 连接__：HDInsight 群集提供 Ambari Web UI。 Ambari Web UI 用于在群集上轻松监视、配置和管理服务。 Storm on HDInsight 还提供了 Storm UI，使用户能够通过浏览器监视和管理正在运行的 Storm 拓扑。

  有关详细信息，请参阅[使用 Ambari Web UI 管理 HDInsight](hdinsight-hadoop-manage-ambari.md) 和[使用 Storm UI 进行监视和管理](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-using-the-storm-ui)。

* __Azure PowerShell 和 CLI__：Azure PowerShell 和 Azure CLI 提供命令行实用工具，可在客户端系统中使用这些工具来操作 HDInsight 和其他 Azure 服务。

* __Visual Studio 集成__：用于 Visual Studio 的 Data Lake 工具包含用于创建 C# Storm 拓扑的项目模板，以及用于监视 Storm on HDInsight 的工具。 可以在 Visual Studio 内部创建、部署、监视和管理 C# 拓扑。

  有关详细信息，请参阅 [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)（使用用于 Visual Studio 的 HDInsight 工具开发 C# Storm 拓扑）。

* __与其他 Azure 服务集成__

  * 为了方便 __Java__ 开发，Microsoft 尽量使用现有 Storm 组件来与其他 Azure 服务集成。 在某些情况下，可能需要特定于服务的组件或解决方案。

    * __Azure Data Lake Store__：基于 Java 的拓扑可以使用 Storm-HDFS Bolt 和 URI 方案 `adl://` 访问 Data Lake Store。 有关使用 Storm-HDFS Bolt 的示例，请参阅 [Use Azure Data Lake Store with Apache Storm on HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-write-data-lake-store)（通过 Apache Storm on HDInsight 使用 Azure Data Lake Store）。

    * __Azure 存储__（用作 HDInsight 的存储时）：基于 Java 的拓扑可以使用 Storm-HDFS Bolt 和 URI 方案 `wasb://` 访问 Azure 存储。

    * __Azure 事件中心__：可以使用 Microsoft 提供的 EventHubSpout 和 EventHubBolt 组件访问。 这些组件是用 Java 编写的，作为独立的 .jar 文件提供。

    有关开发 Java 解决方案的详细信息，请参阅 [Develop a Java-based topology for Storm on HDInsight](hdinsight-storm-develop-java-topology.md)（为 Storm on HDInsight 开发基于 Java 的拓扑）。

  * 对于 __C#__ 开发，通常可以使用适用于 Azure 服务的 .NET SDK。 在某些情况下，SDK 可能依赖于 Linux 上未提供的框架（HDInsight 3.4 或更高版本的主机 OS）。在这种情况下，可在 C# 解决方案内部使用 Java 组件。

    * 有关使用 __SQL 数据库__、__DocumentDB__、__EventHub__ 和 __HBase__ 的示例以模板形式包含在用于 Visual Studio 的 Azure Data Lake 工具中。 有关详细信息，请参阅[为 Storm on HDInsight 开发 C# 拓扑](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

    * __Azure 事件中心__：有关从 C# 解决方案使用 Java 组件的示例，请参阅[使用 Storm on HDInsight 从 Azure 事件中心处理事件 (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)。

    有关开发 C# 解决方案的详细信息，请参阅 [Develop a C# topology for Storm on HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md)（为 Storm on HDInsight 开发 C# 拓扑）。

### <a name="reliability"></a>可靠性

Apache Storm 始终保证每个传入消息受到完全处理，即使数据分析分散在数百个节点。

**Nimbus 节点**提供的功能与 Hadoop JobTracker 类似，它通过 **Zookeeper** 将任务分配给群集中的其他节点。 Zookeeper 节点为群集提供协调功能，并促进 Nimbus 与辅助节点上的 **Supervisor** 进程进行通信。 如果处理的一个节点出现故障，Nimbus 节点将得到通知，并分配到另一个节点的任务和关联的数据。

Apache Storm 的默认配置是只能有一个 Nimbus 节点。 Storm on HDInsight 运行两个 Nimbus 节点。 如果主节点出现故障，HDInsight 群集将切换到辅助节点，同时主节点将会恢复。

![nimbus、zookeeper 和 supervisor 示意图](./media/hdinsight-storm-overview/nimbus.png)

### <a name="scale"></a>缩放

虽然可以在创建过程中指定群集中的节点数，但你可能需要扩大或收缩群集以匹配工作负载。 所有 HDInsight 群集允许你更改群集中的节点数，即使在处理数据时。

> [!NOTE]
> 若要利用通过缩放添加的新节点，你需要重新平衡在增加大小之前启动的拓扑。

### <a name="support"></a>支持

Storm on HDInsight 附带全天候企业级支持。 Storm on HDInsight 也提供 99.9% 的 SLA。 这意味着，我们保证至少 99.9% 的时间群集都能建立外部连接。

## <a name="common-use-cases-for-real-time-analytics"></a>实时分析常见用例

以下是你可能使用 Apache storm on HDInsight 的一些常见方案。 有关实际方案的信息，请参阅 [How companies are using Storm](https://storm.apache.org/documentation/Powered-By.html)（公司如何使用 Storm）。

* 物联网 (IoT)
* 欺诈检测
* 社交分析
* 提取、转换、加载 (ETL)
* 网络监视
* 搜索
* 移动应用场景

## <a name="how-is-data-in-hdinsight-storm-processed"></a>如何处理 HDInsight Storm 中的数据

Apache Storm 运行的是 **拓扑** ，而不是你在 HDInsight 或 Hadoop 中可能熟悉的 MapReduce 作业。 Storm on HDInsight 群集包含两种类型的节点：运行 **Nimbus** 的头节点和运行 **Supervisor** 的辅助角色节点。

* **Nimbus**：类似于 Hadoop 中的 JobTracker，负责在整个群集中分发代码、将任务分配给虚拟机以及监视故障情况。 HDInsight 提供两个 Nimbus 节点，因此 Storm on HDInsight 不会出现单点故障
* **Supervisor**：每个辅助角色节点的 supervisor 负责启动和停止该节点上的**工作进程**。
* **工作进程**：运行**拓扑**的一个子集。 正在运行的拓扑分布在整个群集的许多工作进程上。
* **拓扑**：定义处理数据**流**的计算图形。 与 MapReduce 作业不同，拓扑运行到你停止它们为止。
* **流**：一个未绑定的**元组**集合。 流由 **spout** 和 **bolt** 生成，由 **bolt** 使用。
* **元组**：动态类型化值的一个命名列表。
* **Spout**：使用数据源中的数据，发出一个或多个**流**。
  
  > [!NOTE]
  > 通常情况下，数据是从 Kafka 等队列或 Azure 事件中心读取的。 队列确保发生中断时数据持续不断。

* **Bolt**：使用**流**，处理**元组**，可以发出**流**。 Bolt 还负责将数据编写到外部存储，比如队列、HDInsight HBase、blob 或其他数据存储。
* **Apache Thrift**：用于可缩放跨语言服务开发的软件框架。 可用于构建在 C++、Java、Python、PHP、Ruby、Erlang、Perl、Haskell、C#、Cocoa、JavaScript、Node.js、Smalltalk 及其他语言间工作的服务。

有关 Storm 组件的详细信息，请参阅 apache.org 上的 [Storm 教程][apachetutorial]。

## <a name="what-programming-languages-can-i-use"></a>我可以使用哪些编程语言

### <a name="c35"></a>C&#35;

用于 Visual Studio 的 Data Lake 工具允许 .NET 开发人员以 C# 语言设计和实施拓扑。 你也可以创建使用 Java 和 C# 组件的混合拓扑。

有关详细信息，请参阅 [使用 Visual Studio 开发 Apache Storm on HDInsight 的 C# 拓扑](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

### <a name="java"></a>Java

你遇到的大多数 Java 示例都是无格式 Java 或 Trident。 Trident 是一个高级别抽象，可更轻松地执行联接、汇总、分组和筛选等操作。 但是，Trident 作用于批量元组，其中原始 Java 解决方案一次将处理一个元组流。

有关 Trident 的详细信息，请参阅 apache.org 上的 [Trident 教程](https://storm.apache.org/documentation/Trident-tutorial.html) 。

有关 Java 和 Trident 拓扑的示例，请参阅 [Storm 拓扑示例列表](hdinsight-storm-example-topology.md) 或 HDInsight 群集上的 storm-starter 示例。

storm-starter 示例位于 HDInsight 群集上的 **/usr/hdp/current/storm-client/contrib/storm-starter** 目录中。

### <a name="python"></a>Python

如需 Python 组件的使用示例，请参阅[使用 Python on HDInsight 开发 Storm 拓扑](hdinsight-storm-develop-python-topology.md)。

## <a name="what-are-some-common-development-patterns"></a>常见的开发模式有哪些

### <a name="guaranteed-message-processing"></a>有保证的消息处理

Storm 可以提供不同级别的有保证的消息处理。 例如，基本的 Storm 应用程序至少可以保证一次处理，而 Trident 仅可以保证一次处理。

有关详细信息，请参阅 apache.org 上的 [数据处理保证](https://storm.apache.org/about/guarantees-data-processing.html) 。

### <a name="ibasicbolt"></a>IBasicBolt

读取输入元组，发出零个或多个元组，然后在执行方法结束时立即询问输入元组，这种模式很普通。 Storm 提供 [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) 接口来自动执行这种模式。

### <a name="joins"></a>联接

应用程序之间数据流的联接方式各不相同。 例如，你可以从多个流将每个元组联接到一个新流，也可以仅联接特定窗口的批量元组。 两种方式的联接都可以使用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)来实现，它是定义元组如何路由到 bolt 的方式。

在以下 Java 实例中，fieldsGrouping 用于将来自组件“1”、“2”和“3”的元组路由至 **MyJoiner** bolt。

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>批处理

Storm 提供名为“计时周期元组”的内部计时机制，可用于每隔 X 秒发出一个批处理。

有关从 C# 组件使用计时周期元组的示例，请参阅 [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java)。

如果你使用的是 Trident，则其基于批量处理元组。

### <a name="caching"></a>缓存

内存缓存通常用作加速处理的机制，因为它在内存中存储常用资产。 由于拓扑跨多个节点分布，而每个节点中有多个流程，因此应考虑使用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)。 `fieldsGrouping` 可确保将元组（其中包含的字段用于缓存查找）始终路由到同一进程。 此分组功能可以避免在进程间重复缓存条目。

### <a name="streaming-top-n"></a>流式处理 top N

当拓扑依赖于计算“top N”值时，你应并行计算 top N 值，然后将这些计算的输出合并到全局值中。 为此，可以使用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) 按字段路由以便进行并行处理，然后路由至全局确定前 N 个值的 bolt。

有关计算“top N”值的示例，请参阅 [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) 示例。

## <a name="what-type-of-logging-does-storm-use"></a>Storm 使用哪种类型的日志记录

Storm 使用 Apache Log4j 来记录信息。 默认情况下，将记录大量的数据，因此很难通过信息排序。 可以让日志记录配置文件包括在 Storm 拓扑中，控制日志记录行为。

有关演示如何配置日志记录的示例拓扑，请参阅适用于 HDInsight 上的 Storm 的 [Java-based WordCount](hdinsight-storm-develop-java-topology.md) （基于 Java 的 WordCount）示例。

## <a name="next-steps"></a>后续步骤

了解有关使用 HDInsight 中的 Apache Storm 构建实时分析解决方案的详细信息：

* [Storm on HDInsight 入门][gettingstarted]
* [Storm on HDInsight 的示例拓扑](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

