---
title: 在 Azure 数据工厂中映射数据流性能和优化指南 |Microsoft Docs
description: 了解在使用映射数据流时影响 Azure 数据工厂中数据流的性能的关键因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 09/22/2019
ms.openlocfilehash: e4b3e08c0cc7fc1ead2aed551c228c6a1165c3b6
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180853"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>映射数据流性能和优化指南

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure 数据工厂映射数据流提供了一个无代码浏览器界面，用于大规模设计、部署和协调数据转换。

> [!NOTE]
> 如果你不熟悉 ADF 的一般传输数据流，请参阅阅读本文之前的[数据流概述](concepts-data-flow-overview.md)。
>

> [!NOTE]
> 当你从 ADF UI 设计和测试数据流时，请确保打开调试交换机，以便可以实时执行数据流，而无需等待群集预热。
>

![调试按钮](media/data-flow/debugb1.png "调试")

## <a name="monitor-data-flow-performance"></a>监视数据流性能

在浏览器中设计映射数据流时，可以通过单击每个转换的底部设置窗格中的 "数据预览" 选项卡来对每个单独的转换进行单元测试。 接下来应执行的步骤是在管道设计器中测试端到端数据流。 添加 "执行数据流" 活动，并使用 "调试" 按钮测试数据流的性能。 在管道窗口的底部窗格中，你将看到 "操作" 下的眼镜图标：

数据流![监视器](media/data-flow/mon002.png "数据流 监视器 2")

单击该图标将显示数据流的执行计划和后续性能配置文件。 您可以使用此信息来估算针对不同大小的数据源的数据流的性能。 请注意，你可以在总体性能计算中假设1分钟的群集作业执行设置时间，如果你使用的是默认的 Azure Integration Runtime，则可能还需要添加5分钟的群集启动时间。

数据流![监视](media/data-flow/mon003.png "数据流 监视器 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>针对 Azure SQL 数据库和 Azure SQL 数据仓库进行优化

![源部分](media/data-flow/sourcepart3.png "源部分")

### <a name="partition-your-source-data"></a>对源数据进行分区

* 切换到 "优化" 并选择 "源"。 在查询中设置特定表列或类型。
* 如果选择 "列"，则选取分区列。
* 同时，设置到 Azure SQL DB 的最大连接数。 可以尝试使用更高的设置来获取与数据库的并行连接。 但是，在某些情况下，连接数可能会提高性能。
* 不需要对源数据库表进行分区。
* 在源转换中设置与数据库表的分区方案匹配的查询将允许源数据库引擎利用分区消除。
* 如果源尚未分区，则 ADF 仍将根据你在源转换中选择的键在 Spark 转换环境中使用数据分区。

### <a name="set-batch-size-and-query-on-source"></a>设置源的批大小和查询

![源](media/data-flow/source4.png "源")

* 设置批大小将指示 ADF 将数据存储在内存集中，而不是逐行保存。 这是一个可选设置，如果没有正确调整计算节点的大小，则可能会用尽计算节点上的资源。
* 通过设置查询，可以在数据到达数据流进行处理之前，直接在源中筛选行，从而使初始数据采集更快。
* 如果使用查询，可以为 Azure SQL DB 添加可选的查询提示，即未提交读

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>为 SQL 数据集的源转换设置设置隔离级别

* 未提交读将提供更快的源转换查询结果

![隔离级别](media/data-flow/isolationlevel.png "隔离级别")

### <a name="set-sink-batch-size"></a>设置接收器批大小

![接收器](media/data-flow/sink4.png "接收器")

* 为了避免对数据流进行逐行处理，请在 Azure SQL 数据库的接收器设置中设置 "批大小"。 这会告诉 ADF 按照提供的大小分批处理数据库写入。

### <a name="set-partitioning-options-on-your-sink"></a>设置接收器上的分区选项

* 即使你未将数据分区到目标 Azure SQL DB 表中，也可访问 "优化" 选项卡并设置分区。
* 通常，只需告诉 ADF 在 Spark 执行群集上使用轮循机制分区，可以更快地加载数据，而不是强制从单个节点/分区建立所有连接。

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>在 Azure Integration Runtime 中增加计算引擎的大小

![新 IR](media/data-flow/ir-new.png "新 IR")

* 增加核心数，这会增加节点数量，并提供更多的处理能力，可对 Azure SQL 数据库进行查询和写入。
* 尝试 "计算优化" 和 "内存优化" 选项，将更多资源应用到计算节点。

### <a name="unit-test-and-performance-test-with-debug"></a>带有调试的单元测试和性能测试

* 对数据流进行单元测试时，请将 "数据流调试" 按钮设置为 "开"。
* 在数据流设计器中，使用转换中的 "数据预览" 选项卡来查看转换逻辑的结果。
* 通过在管道设计画布上放置数据流活动，并使用 "调试" 按钮进行测试，对您的数据进行单元测试。
* 在调试模式下测试将适用于实时准备好群集环境，而无需等待实时群集启动。
* 在数据流设计器体验内进行数据预览调试期间，可以通过在数据流设计器 UI 上的 "调试设置" 链接中设置行限制，限制为每个源测试的数据量。 请注意，必须先打开调试模式。

![调试设置](media/data-flow/debug-settings.png "调试设置")

* 在测试管道调试执行中的数据时，可以通过设置每个源的采样大小来限制用于测试的行数。 请确保在按常规操作化计划计划管道时禁用采样。

![行抽样](media/data-flow/source1.png "行抽样")

### <a name="disable-indexes-on-write"></a>在写入时禁用索引
* 在数据流活动之前使用 ADF 管道存储过程活动，可在要从接收器中写入的目标表上禁用索引。
* 在 "数据流" 活动之后，添加另一个启用了这些索引的存储过程活动。

### <a name="increase-the-size-of-your-azure-sql-db"></a>增加 Azure SQL DB 的大小
* 在运行管道之前，计划调整源和接收器 Azure SQL 数据库的大小，以提高吞吐量，并在达到 DTU 限制后将 Azure 限制降到最低。
* 管道执行完成后，可以重新调整数据库的大小，使其恢复到正常运行速度。

## <a name="optimizing-for-azure-sql-data-warehouse"></a>优化 Azure SQL 数据仓库

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>通过 Polybase 使用过渡批量加载数据

* 为了避免对数据流进行逐行处理，请在接收器设置中设置 "过渡" 选项，以使 ADF 能够利用 Polybase 避免按行插入 DW。 这会指示 ADF 使用 Polybase，以便可以批量加载数据。
* 当你从管道执行数据流活动时，如果启用了过渡，你将需要选择用于大容量加载的暂存数据的 Blob 存储区位置。

### <a name="increase-the-size-of-your-azure-sql-dw"></a>增加 Azure SQL DW 的大小

* 在达到 DWU 限制后，在运行管道之前，计划调整源和接收器 Azure SQL DW 的大小，以提高吞吐量并将 Azure 限制降到最低。

* 管道执行完成后，可以重新调整数据库的大小，使其恢复到正常运行速度。

## <a name="optimize-for-files"></a>优化文件

* 可以控制 ADF 将使用的分区数量。 在每个源 & 接收器转换以及每个单独的转换中，可以设置分区方案。 对于较小的文件，可能会发现选择 "单一分区" 有时可以比请求 Spark 分区小型文件更好、更快。
* 如果没有关于源数据的足够信息，可以选择 "轮循机制" 分区并设置分区数。
* 如果您浏览数据并发现您的列可以是良好的哈希键，请使用哈希分区选项。
* 在数据预览和管道调试中进行调试时，请注意，基于文件的源数据集的限制和采样大小仅适用于返回的行数，而不是读取的行数。 这一点很重要，因为它可能会影响调试执行的性能，并且可能会导致流失败。
* 请记住，默认情况下，调试群集是小型单节点群集，因此使用临时小文件进行调试。 使用临时文件，中转到 "调试设置" 并指向数据的一小部分。

![调试设置](media/data-flow/debugsettings3.png "调试设置")

### <a name="file-naming-options"></a>文件命名选项

* 在 ADF 中写入转换后的数据的默认性质是将数据流写入包含 Blob 或 ADLS 链接服务的数据集。 应将该数据集设置为指向文件夹或容器，而不是命名文件。
* 数据流使用 Azure Databricks Spark 执行，这意味着您的输出将基于默认 Spark 分区或您显式选择的分区方案，在多个文件上进行拆分。
* ADF 数据流中的一个非常常见的操作是选择 "输出到单个文件"，以便将所有输出部件文件合并到一个输出文件中。
* 但是，此操作要求输出减小到单个群集节点上的单个分区。
* 选择此常用选项时，请记住这一点。 如果将多个大源文件组合到一个输出文件分区中，则可以用尽群集节点资源。
* 若要避免耗尽计算节点资源，可以保留 ADF 中的默认或显式分区方案，该方案可优化性能，然后在管道中添加后续复制活动，该活动将所有部分文件从输出文件夹合并到新的单个文件. 实质上，此方法将转换操作与文件合并分隔开来，并获得与设置 "输出到单个文件" 相同的结果。

### <a name="looping-through-file-lists"></a>遍历文件列表

在大多数情况下，ADF 中的数据流将从允许数据流源转换循环访问多个文件的管道中更好地执行。 换句话说，在数据流中，首选使用源内部的通配符或文件列表，而不是使用管道中的 ForEach 来循环访问大型文件列表，每次迭代时都要调用 Execute 数据流。 通过允许在数据流内发生循环，数据流进程的执行速度会更快。

例如，如果我的数据文件列表来自7月2019，我想要在 Blob 存储中的某个文件夹中进行处理，则从管道调用一次 "执行数据流" 活动的性能更高，并在源中使用通配符，如下所示:

```DateFiles/*_201907*.txt```

对于管道中的 Blob 存储区，这比查找要好得多，然后使用 ForEach 循环访问所有匹配文件，并在内部使用 "执行数据流" 活动。

### <a name="increase-the-size-of-your-debug-cluster"></a>增加调试群集的大小

默认情况下，打开 "调试" 将使用为每个数据工厂自动创建的默认 Azure 集成运行时。 此默认 Azure IR 设置为8核，4表示驱动程序节点，4表示辅助角色节点，使用常规计算属性。 当你使用较大的数据进行测试时，你可以通过创建具有较大配置的新 Azure IR 来增加调试群集的大小，并在打开调试时选择此新 Azure IR。 这会指示 ADF 使用此 Azure IR 进行数据预览，并使用数据流进行管道调试。

## <a name="next-steps"></a>后续步骤

查看与性能相关的其他数据流文章：

- ["数据流优化" 选项卡](concepts-data-flow-optimize-tab.md)
- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
