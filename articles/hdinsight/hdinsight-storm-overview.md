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
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/31/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1155f534869f240c1567fa8b791dbcb7750c8a40
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Apache Storm on HDInsight 简介：面向 Hadoop 的实时分析

Apache Storm on HDInsight 可让你在 Azure 上创建分布式实时分析解决方案。

Apache Storm 是分布式可容错的开源计算系统，可用于配合 Hadoop 实时处理数据。 Storm 解决方案还提供有保障的数据处理功能，能够重播第一次未成功处理的数据。

## <a name="how-does-storm-work"></a>Storm 的工作原理

Apache Storm 运行的是 **拓扑** ，而不是你在 HDInsight 或 Hadoop 中可能熟悉的 MapReduce 作业。 拓扑由多个以有向无环图 (DAG) 形式排列的组件构成。 下图演示了一个基本单词计数拓扑中组件之间的数据流动方式

![Storm 拓扑中组件排列方式的示例](./media/hdinsight-storm-overview/wordcount-topology.png)

* __Spout__ 组件将数据引入拓扑。 它们将一个或多个流发出到拓扑中

* __Bolt__ 组件使用 Spout 或其他 Bolt 发出的流。 Bolt 可以选择性地将新流发出到拓扑中。 Bolt 还负责将数据写入 HDFS 或 HBase 等持久性存储。

## <a name="why-use-storm-on-hdinsight"></a>为何使用 Storm on HDInsight

Apache Storm on HDInsight 提供以下重要优势：

* 以托管服务的形式执行，提供 99.9% 运行时间 SLA。

* 在群集创建期间或者创建之后，通过针对群集运行脚本轻松进行自定义。 有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

* 使用所选语言：Storm 组件可以使用多种语言编写，例如 **Java**、**C#**、**Python**。

    * Visual Studio 与 HDInsight 集成，可以开发、管理和监视 C# 拓扑。 有关详细信息，请参阅 [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)（使用用于 Visual Studio 的 HDInsight 工具开发 C# Storm 拓扑）。

    * 支持 **Trident** Java 接口。 使用此接口可以创建支持一次性消息处理、事务性数据存储持久性和一组常见流分析操作的 Storm 拓扑。

* 轻松扩展和缩减群集：在不影响 Storm 拓扑运行的情况下添加或删除辅助角色节点。

* 与以下 Azure 服务集成：

    * 事件中心

    * 虚拟网络

    * SQL 数据库

    * Azure 存储

    * DocumentDB。

    * 通过使用 Azure 虚拟网络中安全组合多个 HDInsight 群集的功能：创建使用 HDInsight、HBase 或 Hadoop 群集的分析管道。

有关在实时分析解决方案中使用 Apache Storm 的公司列表，请参阅 [使用 Apache Storm 的公司](https://storm.apache.org/documentation/Powered-By.html)。

若要开始使用 Storm，请参阅 [Storm on HDInsight 入门][gettingstarted]。

## <a name="ease-of-creation"></a>容易创建

你可以在分钟数设置好新的 Storm on HDInsight 群集。 有关创建 Storm 群集的信息，请参阅 [Storm on HDInsight 入门](hdinsight-apache-storm-tutorial-get-started-linux.md)。

## <a name="ease-of-use"></a>易于使用

* __安全外壳连接__：可以使用 SSH 通过 Internet 访问 HDInsight 群集的头节点。 SSH 用于直接在群集上运行命令。

  有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

* __Web 连接__：HDInsight 群集提供 Ambari Web UI。 Ambari Web UI 用于在群集上轻松监视、配置和管理服务。 Storm on HDInsight 还提供了 Storm UI，使用户能够通过浏览器监视和管理正在运行的 Storm 拓扑。

  有关详细信息，请参阅[使用 Ambari Web UI 管理 HDInsight](hdinsight-hadoop-manage-ambari.md) 和[使用 Storm UI 进行监视和管理](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui)。

* __Azure PowerShell 和 CLI__：Azure PowerShell 和 Azure CLI 提供命令行实用工具，可在客户端系统中使用这些工具来操作 HDInsight 和其他 Azure 服务。

* __Visual Studio 集成__：用于 Visual Studio 的 Data Lake 工具包含用于通过 SCP.Net Framework 创建 C# Storm 拓扑的项目模板。 Data Lake 工具还提供用于通过 Storm on HDInsight 部署、监视和管理解决方案的工具。

  有关详细信息，请参阅 [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)（使用用于 Visual Studio 的 HDInsight 工具开发 C# Storm 拓扑）。

## <a name="integration-with-other-azure-services"></a>与其他 Azure 服务集成

* __Azure Data Lake Store__：有关将 Data Lake Store 与 Storm 配合使用的示例，请参阅 [Use Azure Data Lake Store with Apache Storm on HDInsight](hdinsight-storm-write-data-lake-store.md)（将 Azure Data Lake Store 与 Apache Storm on HDInsight 配合使用）。

* __Azure 事件中心__：有关将 Azure 事件中心与 Storm 配合使用的示例，请参阅以下文档：

    * [开发适用于 Storm on HDInsight 的基于 Java 的拓扑](hdinsight-storm-develop-java-topology.md)

    * [使用 Storm on HDInsight 从 Azure 事件中心处理事件 (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* 有关使用 __SQL 数据库__、__DocumentDB__、__EventHub__ 和 __HBase__ 的示例以模板形式包含在用于 Visual Studio 的 Azure Data Lake 工具中。 有关详细信息，请参阅[为 Storm on HDInsight 开发 C# 拓扑](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

## <a name="reliability"></a>可靠性

Apache Storm 始终保证每个传入消息受到完全处理，即使数据分析分散在数百个节点。

**Nimbus 节点**提供的功能与 Hadoop JobTracker 类似，它通过 **Zookeeper** 将任务分配给群集中的其他节点。 Zookeeper 节点为群集提供协调功能，并促进 Nimbus 与辅助节点上的 **Supervisor** 进程进行通信。 如果处理的一个节点出现故障，Nimbus 节点将得到通知，并分配到另一个节点的任务和关联的数据。

Apache Storm 的默认配置是只能有一个 Nimbus 节点。 Storm on HDInsight 运行两个 Nimbus 节点。 如果主节点出现故障，HDInsight 群集将切换到辅助节点，同时主节点将会恢复。

![nimbus、zookeeper 和 supervisor 示意图](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>缩放

虽然可以在创建过程中指定群集中的节点数，但你可能需要扩大或收缩群集以匹配工作负载。 所有 HDInsight 群集允许你更改群集中的节点数，即使在处理数据时。

> [!NOTE]
> 若要利用通过缩放添加的新节点，你需要重新平衡在增加大小之前启动的拓扑。

## <a name="support"></a>支持

Storm on HDInsight 附带全天候企业级支持。 Storm on HDInsight 也提供 99.9% 的 SLA。 这意味着，我们保证至少 99.9% 的时间群集都能建立外部连接。

有关详细信息，请参阅 [Azure 支持](https://azure.microsoft.com/support/options/)。

## <a name="common-use-cases"></a>常见用例

以下是你可能使用 Apache storm on HDInsight 的一些常见方案。 

* 物联网 (IoT)
* 欺诈检测
* 社交分析
* 提取、转换、加载 (ETL)
* 网络监视
* 搜索
* 移动应用场景

有关实际方案的信息，请参阅文档 [How companies are using Storm](https://storm.apache.org/documentation/Powered-By.html)（公司如何使用 Storm）。

## <a name="development"></a>开发

__用于 Visual Studio 的 Data Lake 工具__可让 .NET 开发人员以 __C#__ 语言设计和实施拓扑。 你也可以创建使用 Java 和 C# 组件的混合拓扑。

有关详细信息，请参阅 [使用 Visual Studio 开发适用于 Storm on HDInsight 的 C# 拓扑](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

还可以使用所选的 IDE 开发 __Java__ 解决方案。 有关详细信息，请参阅[开发适用于 Storm on HDInsight 的 Java 拓扑](hdinsight-storm-develop-java-topology.md)。

还可以使用 Python 开发 Storm 组件。 有关详细信息，请参阅[使用 Python on HDInsight 开发 Storm 拓扑](hdinsight-storm-develop-python-topology.md)。

## <a name="common-development-patterns"></a>常见开发模式

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

当拓扑依赖于计算“top”N 值时，你应并行计算 top N 值。 然后将这些计算的输出合并到全局值中。 若要执行此操作，可以使用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) 按字段路由以便进行并行处理，然后路由至全局确定 top N 值的 bolt。

有关计算“top N”值的示例，请参阅 [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) 示例。

## <a name="logging"></a>日志记录

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

