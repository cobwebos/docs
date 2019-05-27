---
title: 在 Azure 数据工厂中的映射数据流的性能和优化指南 |Microsoft Docs
description: 了解如何使用映射数据流时，会影响 Azure 数据工厂中的数据流的性能的关键因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 7fca586083f70e0b0f7e593d5203392260cd2136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172335"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>映射数据的流性能和优化指南

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure 数据工厂映射数据流提供了用于设计、 部署和安排在规模较大的数据转换的无代码的浏览器界面。

> [!NOTE]
> 如果您不熟悉 ADF 映射数据流动一般情况下，请参阅[数据的流概述](concepts-data-flow-overview.md)之前阅读此文章。
>

> [!NOTE]
> 设计和测试 ADF UI 中的数据流时请务必打开调试开关，以便可以执行中数据的流实时而无需等待预热的群集。
>

![调试按钮](media/data-flow/debugb1.png "调试")

## <a name="optimizing-for-azure-sql-database"></a>为 Azure SQL 数据库优化

![源部件](media/data-flow/sourcepart2.png "源部件")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>您可以匹配的 Spark 数据分区到源数据库分区基于源转换中的数据库表列键

* 转到"优化"并选择"源"。 在查询中设置特定的表的列或类型。
* 如果选择"列"，然后选择分区列。
* 此外，设置为 Azure SQL DB 的最大连接数。 你可以尝试更高版本的设置，以获得并行连接到你的数据库。 但是，某些情况下可能会导致有限数目的连接具有更快的性能。

### <a name="set-batch-size-and-query-on-source"></a>在源上设置批大小和查询

![源](media/data-flow/source4.png "源")

* 设置批大小将指示 ADF 将数据存入内存而不是按行中的集。 这是一个可选设置，你可能会耗尽资源在计算节点上如果它们未设置正确大小。
* 设置查询可允许您筛选在源行右侧之前甚至到达的数据流进行处理，这可以使初始数据获取更快。
* 如果使用查询时，您可以为 Azure SQL DB，即 READ UNCOMMITTED 添加可选查询提示

### <a name="set-sink-batch-size"></a>设置接收器批大小

![接收器](media/data-flow/sink4.png "接收器")

* 为了避免数据 floes 的行的处理，Azure SQL DB 的接收器设置中设置"批大小"。 这将告知 ADF 到处理数据库写入分批基于提供的大小。

### <a name="set-partitioning-options-on-your-sink"></a>设置分区在接收器上的选项

* 即使没有对数据进行分区在目标 Azure SQL 数据库表中，转到优化选项卡，并设置分区。
* 经常使用，只需告诉 ADF 使用轮循机制分区上的 Spark 执行群集中要快得多的数据加载而不强制从单个节点/分区的所有连接的结果。

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>增加 Azure 集成运行时中计算引擎的大小

![新的 IR](media/data-flow/ir-new.png "新红外线 （ir)")

* 增加核心，这将增加数量的节点，并为您提供更多处理能力以查询和写入到 Azure SQL DB 的数。
* 请尝试将更多的资源应用于计算节点的"计算优化"和"内存优化"选项。

### <a name="disable-indexes-on-write"></a>在写入禁用索引
* 使用 ADF 管道存储过程活动之前您数据流的活动，以禁用从您的接收器正在写入到在目标表的索引。
* 在数据流活动中之后, 添加启用了这些索引的另一个存储的过程活动。

### <a name="increase-the-size-of-your-azure-sql-db"></a>增加 Azure SQL DB 的大小
* 计划您的源重设大小和管道以增加吞吐量并降低 Azure 限制，一旦达到 DTU 限制在运行前接收器 Azure SQL DB。
* 管道执行完成后，可以调整回其正常运行速率数据库的大小。

## <a name="next-steps"></a>后续步骤
请参阅其他数据流文章：

- [数据流概述](concepts-data-flow-overview.md)
- [数据流活动](control-flow-execute-data-flow-activity.md)

