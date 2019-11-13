---
title: Azure HDInsight 中的高可用性组件
description: HDInsight 群集使用的各种高可用性组件的概述。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 8aeef72c23f3ed40a7f187c976c67c2ae117189a
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958496"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Azure HDInsight 支持的高可用性服务

 为了向你提供分析组件的最佳可用性级别，HDInsight 是使用独特的体系结构开发的，目的是确保关键服务的高可用性（HA）。 此体系结构的某些组件是由 Microsoft 开发的，用于提供自动故障转移。 其他组件是为了支持特定服务而部署的标准 Apache 组件。 本文介绍了 HDInsight 中 HA 服务模型的体系结构，HDInsight 如何支持 HA 服务的故障转移，以及如何从其他服务中断中恢复。

## <a name="high-availability-infrastructure"></a>高可用性基础结构

HDInsight 提供自定义的基础结构，以确保四项主要服务具有自动故障转移功能的高可用性：

- Apache Ambari 服务器
- 适用于 Apache YARN 的应用程序时间线服务器
- Hadoop MapReduce 的作业历史记录服务器
- Apache Livy

此基础结构由许多服务和软件组件组成，其中一些组件由 Microsoft 设计。 以下组件对于 HDInsight 平台是唯一的：

- 从属故障转移控制器
- 主故障转移控制器
- 从属高可用性服务
- 主高可用性服务

![高可用性基础结构](./media/hdinsight-high-availability-components/high-availability-architecture.png)

另外还有其他高可用性服务，即开源 Apache 可靠性组件所支持的服务。 这些组件还存在于 HDInsight 群集上：

- Hadoop 文件系统（HDFS） NameNode
- YARN ResourceManager
- HBase Master

以下部分将提供有关这些服务如何协同工作的详细信息。

## <a name="hdinsight-high-availability-services"></a>HDInsight 高可用性服务

Microsoft 为 HDInsight 群集中下表中的四个 Apache 服务提供支持。 为了将它们与 Apache 中组件支持的高可用性服务区分开来，它们被称为*HDINSIGHT HA 服务*。

| 服务 | 群集节点 | 群集类型 | 目的 |
|---|---|---|---|
| Apache Ambari 服务器| 活动头节点 | All | 监视和管理群集。|
| 适用于 Apache YARN 的应用程序时间线服务器 | 活动头节点 | 除 Kafka 之外的所有 | 维护有关在群集上运行的 YARN 作业的调试信息。|
| Hadoop MapReduce 的作业历史记录服务器 | 活动头节点 | 除 Kafka 之外的所有 | 维护 MapReduce 作业的调试数据。|
| Apache Livy | 活动头节点 | Spark | 允许通过 REST 接口与 Spark 群集轻松交互 |

>[!Note]
> HDInsight 企业安全性套餐（ESP）群集目前仅提供 Ambari 服务器高可用性。

### <a name="architecture"></a>体系结构

每个 HDInsight 群集分别有两个处于活动状态和备用模式下的头节点。 HDInsight HA 服务仅在头节点上运行。 这些服务应始终在活动头节点上运行，并在备用头节点上停止并置于维护模式。

为了保持有效的 HA 服务状态并提供快速故障转移，HDInsight 利用了 Apache ZooKeeper，这是分布式应用程序的协调服务，用于执行活动的头节点选举。 HDInsight 还预配了几个后台 Java 进程，用于协调 HDInsight HA 服务的故障转移过程。 这些服务如下所示：主故障转移控制器、从属故障转移控制器、*主-ha 服务*和*从属 ha 服务*。

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper 是适用于分布式应用程序的高性能协调服务。 在生产环境中，ZooKeeper 通常在复制模式下运行，其中 ZooKeeper 服务器的复制组构成仲裁。 每个 HDInsight 群集都有三个 ZooKeeper 节点，它们允许三台 ZooKeeper 服务器形成仲裁。 HDInsight 有两个相互并行运行的 ZooKeeper 仲裁。 一个仲裁决定了 HDInsight HA 服务应在其上运行的群集中的活动头节点。 使用另一个仲裁来协调 Apache 提供的 HA 服务，如后面的部分中所述。

### <a name="slave-failover-controller"></a>从属故障转移控制器

从属故障转移控制器在 HDInsight 群集中的每个节点上运行。 此控制器负责在每个节点上启动 Ambari 代理和*从属 ha 服务*。 它会定期查询有关活动头节点的第一个 ZooKeeper 仲裁。 当活动和备用头节点发生变化时，从属故障转移控制器会执行以下操作：

1. 更新主机配置文件。
1. 重新启动 Ambari 代理。

*从属 ha-服务*负责停止备用头节点上的 HDInsight ha 服务（Ambari 服务器除外）。

### <a name="master-failover-controller"></a>主故障转移控制器

主故障转移控制器在这两个头节点上运行。 这两个主故障转移控制器都与第一个 ZooKeeper 仲裁通信，以将它们正在运行的头节点表示为活动头节点。

例如，如果头节点0上的主故障转移控制器入选选举，则会发生以下更改：

1. 头节点0变为活动状态。
1. 主故障转移控制器在头节点0上启动 Ambari server 和*master-ha 服务*。
1. 其他主故障转移控制器在头节点1上停止 Ambari 服务器和*主-ha 服务*。

Master-ha 服务仅在活动头节点上运行，它会停止备用头节点上的 HDInsight HA 服务（Ambari 服务器除外），并在活动头节点上启动它们。

### <a name="the-failover-process"></a>故障转移过程

![故障转移过程](./media/hdinsight-high-availability-components/failover-steps.png)

运行状况监视器在每个头节点上运行，并将检测信号通知发送到 Zookeeper 仲裁。 在此方案中，头节点被视为 HA 服务。 运行状况监视器会检查每个高可用性服务是否正常运行，以及是否已准备好加入领导选举。 如果是，则此头节点将在选举中竞争。 如果不是，则会退出选举，直到它再次就绪。

如果备用头节点实现了领导，并且变为活动状态（例如，在以前的活动节点出现故障的情况下），则其主故障转移控制器将在其上启动所有 HDInsight HA 服务。 主故障转移控制器还将在其他头节点上停止这些服务。

对于 HDInsight HA 服务故障（如服务关闭或运行不正常），主故障转移控制器应根据头节点状态自动重新启动或停止服务。 用户不应在这两个头节点上手动启动 HDInsight HA 服务。 相反，允许自动或手动故障转移来帮助服务恢复。

### <a name="inadvertent-manual-intervention"></a>意外手动干预

HDInsight HA 服务应仅在活动头节点上运行，并在必要时自动重新启动。 由于单个 HA 服务没有自己的运行状况监视器，因此无法在单个服务级别触发故障转移。 在节点级别（而不是服务级别）确保故障转移。

### <a name="some-known-issues"></a>一些已知问题

- 在备用头节点上手动启动 HA 服务时，它不会停止，直到下一次故障转移。 当 HA 服务同时在这两个头节点上运行时，一些潜在问题包括： Ambari UI 不可访问、Ambari 引发错误、YARN、Spark 和 Oozie 作业可能会停滞。

- 当活动头节点上的 HA 服务停止时，它不会重新启动，直到下一次故障转移或主故障转移控制器/主-HA 服务重新启动。 当活动头节点上的一个或多个 HA 服务停止时，尤其是当 Ambari 服务器停止时，Ambari UI 不可访问，其他潜在问题包括 YARN、Spark 和 Oozie 作业故障。

## <a name="apache-high-availability-services"></a>Apache 高可用性服务

Apache 为 HDFS NameNode、YARN ResourceManager 和 HBase Master 提供高可用性，这些功能在 HDInsight 群集中也可用。 与 HDInsight HA 服务不同，它们在 ESP 群集中受支持。 Apache HA 服务与第二个 ZooKeeper 仲裁（如上一节所述）进行通信，以选择活动/待机状态并执行自动故障转移。 以下部分详细说明了这些服务的工作原理。

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop 分布式文件系统（HDFS） NameNode

基于 Apache Hadoop 2.0 或更高版本的 HDInsight 群集提供 NameNode 高可用性。 头节点上运行了两个 Namenode，它们配置为自动故障转移。 Namenode 使用*ZKFailoverController*与 Zookeeper 进行通信，以选择活动/备用状态。 *ZKFailoverController*在这两个头节点上运行，其工作方式与上述主故障转移控制器相同。

第二个 Zookeeper 仲裁独立于第一个仲裁，因此活动 NameNode 不能在活动头节点上运行。 当活动 NameNode 处于不正常状态或不正常时，备用 NameNode 将入选选举并变成活动状态。

### <a name="yarn-resourcemanager"></a>YARN ResourceManager

基于 Apache Hadoop 2.4 或更高版本的 HDInsight 群集支持 YARN ResourceManager 高可用性。 在头节点0和头节点1上分别运行了两个 ResourceManagers： rm1 和 rm2。 与 NameNode 一样，YARN ResourceManager 还配置为自动故障转移。 当当前的活动 ResourceManager 关闭或不响应时，会自动选择另一个 ResourceManager 处于活动状态。

YARN ResourceManager 使用其嵌入的*ActiveStandbyElector*作为故障检测器和领导者 elector。 与 HDFS NodeManager 不同，YARN ResourceManager 不需要单独的 ZKFC 守护程序。 活动 ResourceManager 将其状态写入 Apache Zookeeper。

YARN ResourceManager 的高可用性与 NameNode 和其他 HDInsight HA 服务无关。 活动 ResourceManager 可能不会在活动 NameNode 正在运行的活动头节点或头节点上运行。 有关 YARN ResourceManager 高可用性的详细信息，请参阅[Resourcemanager 高可用性](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)。

### <a name="hbase-master"></a>HBase Master

HDInsight HBase 群集支持 HBase Master 高可用性。 与在头节点上运行的其他 HA 服务不同，HBase 主机在三个 Zookeeper 节点上运行，其中一个节点是活动的主节点，另两个节点是备用的。 与 NameNode 一样，HBase Master 与 Apache Zookeeper 进行领导选举，并在当前活动的主节点出现问题时进行自动故障转移。 在任何时候都只有一个活动 HBase Master。

## <a name="next-steps"></a>后续步骤

- [HDInsight 中的 Apache Hadoop 群集的可用性和可靠性](hdinsight-high-availability-linux.md)
- [Azure HDInsight 虚拟网络体系结构](hdinsight-virtual-network-architecture.md)
