---
title: Azure HDInsight 高度可用的解决方案体系结构案例研究
description: 本文是对可能的 Azure HDInsight 高度可用的解决方案体系结构的虚构案例研究。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop 高可用性
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 4b98b03c2d7eb4a0403b4595c1376656ed42511b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855032"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Azure HDInsight 高度可用的解决方案体系结构案例研究

Azure HDInsight 的复制机制可集成到高可用性解决方案体系结构中。 在本文中，Contoso 零售的虚构案例研究用于说明可能的高可用性灾难恢复方法、成本注意事项及其相应的设计。

高可用性灾难恢复建议可以有许多排列和组合。 这些解决方案将在 deliberating 每个选项的优点和缺点后收到。 本文仅讨论一种可能的解决方案。

## <a name="customer-architecture"></a>客户体系结构

下图描绘了 Contoso 零售主体系结构。 该体系结构包含流式处理工作负荷、批处理工作负荷、服务层、消耗层、存储层和版本控制。

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Contoso 零售体系结构":::

### <a name="streaming-workload"></a>流式处理工作负载

设备和传感器会将数据生成到 HDInsight Kafka，后者构成消息传递框架。 An HDInsight Spark 使用者从 Kafka 主题读取。 Spark 会转换传入的消息，并将其写入到服务层上的 HDInsight HBase 群集。

### <a name="batch-workload"></a>批处理工作负荷

An HDInsight Hadoop 群集运行 Hive 和 MapReduce 引入本地事务性系统中的数据。 Hive 和 MapReduce 转换的原始数据存储在由 Azure Data Lake Storage Gen2 支持的 data lake 逻辑分区上的 Hive 表中。 存储在 Hive 表中的数据也可用于 Spark SQL，这会在将特选数据存储在 HBase 中之前进行批处理转换。

### <a name="serving-layer"></a>服务层

使用 Apache Phoenix An HDInsight HBase 群集用于向 web 应用程序和可视化仪表板提供数据。 An HDInsight LLAP 群集用于满足内部报告要求。

### <a name="consumption-layer"></a>消耗层

Azure API 应用和 API 管理层返回面向公众的网页。 内部报告要求通过 Power BI 来完成。

### <a name="storage-layer"></a>存储层

逻辑分区 Azure Data Lake Storage Gen2 用作企业 Data Lake。 HDInsight 元存储由 Azure SQL DB 支持。

### <a name="version-control-system"></a>版本控制系统

集成到 Azure Pipelines 并在 Azure 外部托管的版本控制系统。

## <a name="customer-business-continuity-requirements"></a>客户业务连续性要求

如果发生灾难，确定所需的最小业务功能是非常重要的。

### <a name="contoso-retails-business-continuity-requirements"></a>Contoso 零售的业务连续性要求

* 必须防范区域性故障或区域服务运行状况问题。
* 我的客户永远不会看到404错误。 必须始终提供公共内容。  (RTO = 0)   
* 在一年中的大部分时间，我们可以显示过时的公共内容5小时。  (RPO = 5 小时) 
* 在节日期间，面向公众的内容必须始终保持最新状态。  (RPO = 0) 
* 我的内部报告要求对于业务连续性并不重要。
* 优化业务连续性成本。

## <a name="proposed-solution"></a>建议的解决方案

下图显示了 Contoso 零售的高可用性灾难恢复体系结构。

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Contoso 零售体系结构" 模型来确保始终提供数据，而不考虑区域和 RPO 为零。

如果主要区域中发生区域性故障，则会在5小时内从次要区域提供网页和后端内容，并且会出现一定程度的过期。 如果 Azure 服务运行状况仪表板在5小时的时段内未指示恢复 ETA，Contoso 零售将在次要区域中创建 Hive 和 Spark 转换层，然后将所有上游数据源指向次要区域。 使辅助区域成为可写的会导致故障回复过程，该过程涉及复制回主要副本。

在购物高峰期间，整个辅助管道始终处于活动状态且正在运行。 Kafka 生成方将生成两个区域，HBase 复制将从 Leader-Follower 更改为 Leader-Leader，以确保公开面向公众的内容始终处于最新状态。

不需要为内部报告设计故障转移解决方案，因为它对于业务连续性并不重要。

## <a name="next-steps"></a>后续步骤

若要详细了解本文中所述的项，请参阅：

* [Azure HDInsight 业务连续性](./hdinsight-business-continuity.md)
* [Azure HDInsight 业务连续性体系结构](./hdinsight-business-continuity-architecture.md)
* [什么是 Azure HDInsight 中的 Apache Hive 和 HiveQL？](./hadoop/hdinsight-use-hive.md)