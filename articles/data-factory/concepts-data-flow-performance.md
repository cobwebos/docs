---
title: 在 Azure 数据工厂中的映射数据流的性能和优化指南 |Microsoft Docs
description: 了解如何使用映射数据流时，会影响 Azure 数据工厂中的数据流的性能的关键因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 46be01c57be0e4f5fa74f8e8b0d91db3d78f441c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480413"
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

## <a name="monitor-data-flow-performance"></a>监视数据流性能

设计您的映射数据的流在浏览器中，您可以单元测试每个单独的转换通过单击数据预览选项卡在底部设置窗格中的每个转换。 应采取的下一步是在管道设计器中测试你的数据流端到端。 添加执行数据流活动并使用调试按钮以测试数据的流的性能。 在管道窗口的底部窗格中，您将看到在"操作"下的一个眼镜图标：

![数据流监视器](media/data-flow/mon002.png "数据流监视器 2")

单击该图标将显示执行计划和后续的性能配置文件的数据的流。 可以使用此信息来估计数据的流对不同大小的数据源的性能。 请注意，可以在您的整体性能计算假设群集作业执行设置时间的 1 分钟，如果使用默认 Azure 集成运行时，可能需要添加群集启动时的 5 分钟。

![数据流监视](media/data-flow/mon003.png "数据流监视器 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>针对 Azure SQL 数据库和 Azure SQL 数据仓库进行优化

![源部件](media/data-flow/sourcepart2.png "源部件")

### <a name="partition-your-source-data"></a>将源数据分区

* 转到"优化"并选择"源"。 在查询中设置特定的表的列或类型。
* 如果选择"列"，然后选择分区列。
* 此外，设置为 Azure SQL DB 的最大连接数。 你可以尝试更高版本的设置，以获得并行连接到你的数据库。 但是，某些情况下可能会导致有限数目的连接具有更快的性能。
* 不需要对源数据库表进行分区。
* 设置查询源转换相匹配的数据库表的分区方案中，要利用分区排除的源数据库引擎。
* 如果您的源未分区，ADF 将仍使用 Spark 转换环境选择源转换中的关键字中的数据分区。

### <a name="set-batch-size-and-query-on-source"></a>在源上设置批大小和查询

![源](media/data-flow/source4.png "源")

* 设置批大小将指示 ADF 将数据存入内存而不是按行中的集。 这是一个可选设置，你可能会耗尽资源在计算节点上如果它们未设置正确大小。
* 设置查询可允许您筛选在源行右侧之前甚至到达的数据流进行处理，这可以使初始数据获取更快。
* 如果使用查询时，您可以为 Azure SQL DB，即 READ UNCOMMITTED 添加可选查询提示

### <a name="set-sink-batch-size"></a>设置接收器批大小

![接收器](media/data-flow/sink4.png "接收器")

* 为了避免你数据的流的行的处理，Azure SQL DB 的接收器设置中设置"批大小"。 这将告知 ADF 到处理数据库写入分批基于提供的大小。

### <a name="set-partitioning-options-on-your-sink"></a>设置分区在接收器上的选项

* 即使没有对数据进行分区在目标 Azure SQL 数据库表中，转到优化选项卡，并设置分区。
* 经常使用，只需告诉 ADF 使用轮循机制分区上的 Spark 执行群集中要快得多的数据加载而不强制从单个节点/分区的所有连接的结果。

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>增加 Azure 集成运行时中计算引擎的大小

![新的 IR](media/data-flow/ir-new.png "新红外线 （ir)")

* 增加核心，这将增加数量的节点，并为您提供更多处理能力以查询和写入到 Azure SQL DB 的数。
* 请尝试将更多的资源应用于计算节点的"计算优化"和"内存优化"选项。

### <a name="unit-test-and-performance-test-with-debug"></a>具有调试单元测试和性能测试

* 当单元测试的数据流，设置为 ON 的"调试流数据"按钮。
* 在数据流设计器中，使用数据预览选项卡上转换查看转换逻辑的结果。
* 单元测试你的数据管道设计器从流通过将数据流活动放置在管道设计画布，然后使用"调试"按钮来测试。
* 在调试模式下测试将起作用而无需等待在实时群集启动一个实时上做好准备群集环境。

### <a name="disable-indexes-on-write"></a>在写入禁用索引
* 使用 ADF 管道存储过程活动之前您数据流的活动，以禁用从您的接收器正在写入到在目标表的索引。
* 在数据流活动中之后, 添加启用了这些索引的另一个存储的过程活动。

### <a name="increase-the-size-of-your-azure-sql-db"></a>增加 Azure SQL DB 的大小
* 计划您的源重设大小和管道以增加吞吐量并降低 Azure 限制，一旦达到 DTU 限制在运行前接收器 Azure SQL DB。
* 管道执行完成后，可以调整回其正常运行速率数据库的大小。

## <a name="optimizing-for-azure-sql-data-warehouse"></a>优化 Azure SQL 数据仓库

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>使用临时过程来批量通过 Polybase 将数据加载

* 为了避免你数据的流的行的处理，"过渡"选项中设置接收器设置，以便 ADF 可以利用以避免由行插入到数据仓库的 Polybase。 这将指示 ADF，若要使用 Polybase，以便可以在大容量加载数据。
* 当执行从您数据的流活动的管道，使用临时过程开启，将需要选择用于大容量加载临时数据的 Blob 存储位置。

### <a name="increase-the-size-of-your-azure-sql-dw"></a>增加 Azure SQL 数据仓库的大小

* 计划您的源的大小调整和接收器 Azure SQL 数据仓库之前运行管道以增加吞吐量并降低 Azure 限制，一旦达到 DWU 限制。

* 管道执行完成后，可以调整回其正常运行速率数据库的大小。

## <a name="optimize-for-files"></a>优化的文件

* 您可以控制 ADF 将使用的分区数量。 每个源和接收器的转换，以及每个单独的转换，您可以设置分区方案。 对于较小的文件，可能会发现更好地又比询问 Spark 进行分区小文件更快地选择"单一分区"有时处理。
* 如果源数据没有足够的信息，可以选择"轮循机制"分区和设置的分区数。
* 如果您浏览你的数据并发现有一个可以是好的哈希键的列，使用哈希分区选项。

### <a name="file-naming-options"></a>文件命名选项

* ADF 映射数据流中写入转换后的数据的默认特性是要写入到具有 Blob 或 ADLS 链接服务的数据集。 应设置该数据集，以指向一个文件夹或容器，不是命名的文件。
* 数据的流，则使用 Azure Databricks Spark 执行，这意味着，将对基于多个文件拆分输出或者默认分区的 Spark 或分区方案，您已显式选择。
* ADF 数据流中的很常见操作是选择"输出到单个文件"，使所有输出一部分文件时，将合并到单个输出文件。
* 但是，此操作需要输出，可以减少到单个群集节点上的单个分区。
* 选择此常用选项时，请记住这一点。 如果要合并到单个输出文件分区的多个大型源代码文件，可以运行群集节点资源不足。
* 若要避免耗尽计算节点资源，您可以在 ADF 中，这会针对性能进行优化，保留默认值或显式分区方案，然后再添加合并中的所有部分的管道中的后续复制活动从输出文件夹文件到新的单文件。 从根本上来说，此方法将从文件合并转换的操作，并实现相同的结果与设置"输出到单个文件"。

## <a name="next-steps"></a>后续步骤
请参阅其他数据流文章：

- [数据流概述](concepts-data-flow-overview.md)
- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
