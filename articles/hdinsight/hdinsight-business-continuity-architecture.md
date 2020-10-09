---
title: Azure HDInsight 业务连续性体系结构
description: 本文介绍适用于 HDInsight 的不同业务连续性体系结构
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop 高可用性
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 9eb0cd3fd327a53dd0761779916caa096153a010
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856426"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Azure HDInsight 业务连续性体系结构

本文提供了几个适用于 Azure HDInsight 的业务连续性体系结构示例。 在发生灾难时，降低功能的容差是从一个应用程序到下一个应用程序的业务决策。 某些应用程序不可用或在某个时间段内降低功能或延迟处理可能是可接受的。 对于其他应用程序，任何缩减的功能都可能是不可接受的。

> [!NOTE]
> 本文中所述的体系结构没有详尽介绍。 你应设计自己的独特体系结构，一旦你制定了关于预期业务连续性、运营复杂性和所有权成本的客观决定。

## <a name="apache-hive-and-interactive-query"></a>Apache Hive 和交互式查询

在 HDInsight Hive 和交互式查询群集中，建议使用[Hive 复制 V2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS)来实现业务连续性。 需要复制的独立 Hive 群集的永久部分为存储层和 Hive 元存储。 具有企业安全性套餐的多用户方案中的 Hive 群集需要 Azure Active Directory 域服务和 Ranger 元存储。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导" 会将整个 Hive 仓库（包括从主副本 Hive 元存储信息）复制到辅助副本。

* 增量运行在主群集上自动运行，在增量运行期间生成的事件将在辅助群集上播放。 辅助群集与从主群集生成的事件相结合，从而确保辅助群集与复制运行后的主要群集事件一致。

仅当复制运行分布式副本时才需要辅助群集 `DistCp` ，但存储和元存储需要是永久性的。 你可以选择在复制之前按需启动脚本辅助群集，对其运行复制脚本，然后在成功复制后将其关闭。

辅助群集通常是只读的。 可以将辅助群集设置为读写，但这会增加额外的复杂性，涉及将更改从辅助群集复制到主群集。

### <a name="hive-event-based-replication-rpo--rto"></a>基于 Hive 事件的复制 RPO & RTO

* RPO：数据丢失限制为从主站点到辅助副本的最后一个成功的增量复制事件。

* RTO：上游和下游事务与辅助副本之间的故障与恢复之间的时间。

### <a name="apache-hive-and-interactive-query-architectures"></a>Apache Hive 和交互式查询体系结构

#### <a name="hive-active-primary-with-on-demand-secondary"></a>具有按需辅助副本的 Hive 活动主节点

在 *具有按需辅助* 体系结构的活动主节点中，应用程序将写入活动的主要区域，而在正常操作过程中，不会在次要区域中预配任何群集。 辅助区域中的 SQL 元存储和存储是永久性的，而 HDInsight 群集只在计划的 Hive 复制运行之前按需编写和部署。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

#### <a name="hive-active-primary-with-standby-secondary"></a>带有备用辅助副本的 Hive 活动主节点

在 *带有备用辅助副本的活动主*区域中，应用程序将写入活动的主要区域，而只读模式下的备用缩减辅助群集会在正常操作期间运行。 在正常操作过程中，可以选择将区域特定的读取操作卸载到辅助数据库。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

## <a name="apache-spark"></a>Apache Spark

Spark 工作负荷不一定涉及 Hive 组件。 为了使 Spark SQL 工作负荷能够从 Hive 读取和写入数据，HDInsight Spark 群集在同一区域中与 Hive/Interactive 查询群集共享 Hive 自定义元存储。 在这种情况下，Spark 工作负载的跨区域复制还必须伴随 Hive 元存储和存储的复制。 本部分中的故障转移方案适用于这两种情况：

* [使用 Hive 仓库连接器 (HWC 使用 HDInsight 交互式查询群集在 ACID 表上 SPARK SQL) 安装程序](./interactive-query/apache-hive-warehouse-connector.md) 。
* 使用 HDInsight Hadoop 群集在非 ACID 表上 Spark SQL 工作负荷。

对于在独立模式下运行的应用程序，需要使用 Azure 数据工厂定期将 Livy) 作业的特选数据和存储的 Spark Jar (从主要区域复制到次要区域 `DistCP` 。

建议你使用版本控制系统来存储 Spark 笔记本和库，它们可在主要或次要群集上轻松部署。 确保在主工作区或辅助工作区中加载正确的数据，以确保基于笔记本的解决方案和非笔记本解决方案都已准备就绪。

如果有客户特定的库，这些库超出了 HDInsight 本机提供的功能，则必须对其进行跟踪，并定期将其加载到备用辅助群集。  

### <a name="apache-spark-replication-rpo--rto"></a>& RTO Apache Spark 复制 RPO

* RPO：数据丢失限制为最后一次成功的增量复制 (Spark 和 Hive) 从主数据库到辅助副本。

* RTO：上游和下游事务与辅助副本之间的故障与恢复之间的时间。

### <a name="apache-spark-architectures"></a>Apache Spark 体系结构

#### <a name="spark-active-primary-with-on-demand-secondary"></a>具有按需辅助数据库的 Spark 活动主节点

在正常操作过程中，应用程序在主要区域中的 Spark 和 Hive 群集上进行读取和写入，而不会在次要区域中设置群集。 SQL 元存储、Hive 存储和 Spark 存储在次要区域中是永久性的。 Spark 和 Hive 群集按需编写和部署。 Hive 复制用于复制 Hive 存储和 Hive 元存储，而 Azure 数据工厂 `DistCP` 可用于复制独立 Spark 存储。 在每个 Hive 复制运行之前，需要部署 hive 群集，因为依赖项 `DistCp` 计算。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

#### <a name="spark-active-primary-with-standby-secondary"></a>带有备用辅助数据库的 Spark 活动主节点

应用程序对主区域中的 Spark 和 Hive 群集进行读取和写入，而处于只读模式下的备用扩展配置单元和 Spark 群集在正常操作过程中运行于辅助区域。 在正常操作过程中，可以选择将区域特定的 Hive 和 Spark 读取操作卸载到辅助数据库。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

## <a name="apache-hbase"></a>Apache HBase

HBase 导出和 HBase 复制是在 HDInsight HBase 群集之间实现业务连续性的常用方法。

HBase 导出是一个批处理复制过程，该过程使用 HBase 导出实用程序将表从主 HBase 群集导出到其基础 Azure Data Lake Storage 第2代存储。 然后，可以从辅助 HBase 群集访问导出的数据，并将其导入到必须 preexist 在辅助站点中的表中。 尽管 HBase 导出功能提供表级别的粒度，但在增量更新情况下，导出自动化引擎控制每次运行时要包含的增量行范围。 有关详细信息，请参阅 [HDInsight HBase 备份和复制](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#export-then-import)。

HBase 复制以完全自动的方式在 HBase 群集之间使用近乎实时的复制。 复制是在表级别上完成的。 所有表或特定表都可以作为复制的目标。 HBase 复制最终是一致的，这意味着对主要区域中的表进行的最新编辑操作可能无法立即用于所有辅助副本。 辅助副本最终会与主副本保持一致。 如果以下情况，可以在两个或多个 HDInsight HBase 群集之间设置 HBase 复制：

* 主数据库和辅助数据库位于同一虚拟网络中。
* 主数据库和辅助数据库位于同一区域中的不同对等互连 Vnet。
* 主数据库和辅助数据库位于不同区域的不同对等互连 Vnet 中。

有关详细信息，请参阅 [在 Azure 虚拟网络中设置 Apache HBase 群集复制](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-replication)。

还有其他几种方式来执行 HBase 群集的备份，如 [复制 hbase 文件夹](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-the-hbase-folder)、 [复制表](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-tables) 和 [快照](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#snapshots)。

### <a name="hbase-rpo--rto"></a>HBase RPO & RTO

#### <a name="hbase-export"></a>HBase 导出

* RPO：数据丢失限制为第一次成功的批处理增量导入主要副本。
* RTO：主要和恢复辅助数据库上的 i/o 操作失败之间的时间。

#### <a name="hbase-replication"></a>HBase 复制

* RPO：数据丢失仅限于在辅助副本上收到的最后一个 WalEdit 发货。
* RTO：主要和恢复辅助数据库上的 i/o 操作失败之间的时间。

### <a name="hbase-architectures"></a>HBase 体系结构

可以在三种模式下设置 HBase 复制：引导 Leader-Leader 和循环。

#### <a name="hbase-replication--leader--follower-model"></a>HBase 复制：领导者–从后模型

在此跨区域设置中，复制从主要区域复制到次要区域。 可以为单向复制标识主副本上的所有表或特定表。 在正常操作期间，辅助群集可用于在其自己的区域中提供读取请求。

辅助群集作为一般 HBase 群集运行，可以托管其自身的表，并且可以提供区域应用程序的读取和写入操作。 但是，对复制的表或本机到辅助数据库的表的写入不会复制回主副本。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

#### <a name="hbase-replication--leader--leader-model"></a>HBase 复制：领导者–领导者模型

这种跨区域设置非常类似于单向设置，只不过复制发生在主要区域和次要区域之间双向。 应用程序可以在读写模式下使用两个群集，并且更新在它们之间异步交换。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>HBase 复制：多区域或循环

多区域/循环复制模型是 HBase 复制的扩展，可用于创建包含多个应用程序的全局冗余 HBase 体系结构，这些应用程序可读取和写入区域特定 HBase 群集。 根据业务要求，可以根据业务要求，在负责人/领导者或组长/后的各种组合中设置群集。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

## <a name="apache-kafka"></a>Apache Kafka

若要启用跨区域可用性，HDInsight 4.0 支持 Kafka MirrorMaker，可用于在不同区域中维护主要 Kafka 群集的辅助副本。 MirrorMaker 充当高级使用者的使用者对，使用主群集中的特定主题，并使用同一名称在辅助副本中生成。 使用 MirrorMaker 进行跨群集复制以实现高可用性灾难恢复，假设创建者和使用者需要故障转移到副本群集。 有关详细信息，请参阅 [使用 MirrorMaker 在 HDInsight 上使用 Kafka 复制 Apache Kafka 主题](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-mirroring)

根据启动复制时的主题生存期，MirrorMaker 主题复制可能会导致源和副本主题之间出现不同的偏移量。 HDInsight Kafka 群集还支持主题分区复制，这是单个群集级别的高可用性功能。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

### <a name="apache-kafka-architectures"></a>Apache Kafka 体系结构

#### <a name="kafka-replication-active--passive"></a>Kafka 复制：主动-被动

Active-Passive 安装程序允许从活动到被动的异步单向镜像。 制造者和使用者需要知道是否存在主动和被动群集，并且在活动失败时必须准备好将故障转移到被动。 下面是 Active-Passive 安装程序的一些优点和缺点。

优点：

* 群集之间的网络延迟不会影响活动群集的性能。
* 简单的单向复制。

缺点：

* 被动群集可能仍未充分利用。
* 设计复杂性：将故障转移感知纳入应用程序创建者和使用者。
* 在活动群集出现故障期间可能会丢失数据。
* 主动和被动群集之间的主题之间的最终一致性。
* 故障回复到主要副本可能会导致主题中出现消息不一致的情况。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

#### <a name="kafka-replication-active--active"></a>Kafka 复制：活动–活动

Active-Active 设置涉及两个突破独立的 VNet 对等互连 HDInsight Kafka 群集，其中包含带有 MirrorMaker 的双向异步复制。 在此设计中，主要用户使用的消息也可以在辅助数据库中使用，反之亦然。 下面是 Active-Active 安装程序的一些优点和缺点。

优点：

* 由于故障转移和故障回复的重复状态，因此更易于执行。

缺点：

* 设置、管理和监视比主动-被动更复杂。
* 循环复制的问题需要解决。  
* 双向复制会导致更高的区域数据出口费用。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

## <a name="hdinsight-enterprise-security-package"></a>HDInsight 企业安全性套餐

此设置用于在主副本和辅助副本中启用多用户功能，以及 [AZURE AD DS 副本集](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-replica-set) ，以确保用户可以对这两个群集进行身份验证。 在正常操作过程中，需要在辅助副本中设置 Ranger 策略，以确保将用户限制为读取操作。 下面的体系结构说明启用了 ESP 的配置单元活动主要-备用辅助设置可能的外观。

Ranger 元存储复制：

Ranger 元存储用于永久存储和提供 Ranger 策略来控制数据授权。 建议在主数据库和辅助数据库中维护独立的 Ranger 策略，并将辅助数据库作为读取副本维护。
  
如果要求在主和辅助数据库之间保持 Ranger 策略同步，请使用 [Ranger 导入/导出](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export#:~:text=Ranger%20has%20introduced%20a%20new,can%20import%20and%20export%20policies.&text=Also%20can%20export%2Fimport%20a,repositories\)%20via%20Ranger%20Admin%20UI) 定期备份，并将 Ranger 策略从主副本导入到辅助副本。

如果在主数据库和辅助数据库之间复制 Ranger 策略，则可能导致辅助数据库变为启用写入，这可能会导致在辅助数据库上意外写入，导致数据不一致。  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="Hive 和交互式查询体系结构&quot;:::

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 这包括两个不同的阶段：引导和增量运行：

* &quot;引导":::

## <a name="next-steps"></a>后续步骤

若要详细了解本文中所述的项，请参阅：

* [Azure HDInsight 业务连续性](./hdinsight-business-continuity.md)
* [Azure HDInsight 高度可用的解决方案体系结构案例研究](./hdinsight-high-availability-case-study.md)
* [什么是 Azure HDInsight 中的 Apache Hive 和 HiveQL？](./hadoop/hdinsight-use-hive.md)