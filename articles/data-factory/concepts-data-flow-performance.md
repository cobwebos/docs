---
title: 在 Azure 数据工厂中映射数据流性能和优化指南 |Microsoft Docs
description: 了解影响 Azure 数据工厂中映射数据流的性能的关键因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 10/07/2019
ms.openlocfilehash: 24b0deb60f1047228dc3ff6000d423e7cb6939ca
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72387323"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>映射数据流性能和优化指南

在 Azure 数据工厂中映射数据流提供了一个无代码界面，用于大规模设计、部署和协调数据转换。 如果不熟悉如何映射数据流，请参阅[映射数据流概述](concepts-data-flow-overview.md)。

从 ADF UX 设计和测试数据流时，请确保在调试模式下实时执行数据流，而无需等待群集预热。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

## <a name="monitoring-data-flow-performance"></a>监视数据流性能

设计映射数据流时，可以通过单击 "配置" 面板中的 "数据预览" 选项卡对每个转换进行单元测试。 验证逻辑后，在管道中将数据流作为活动的端到端进行测试。 添加 "执行数据流" 活动，并使用 "调试" 按钮测试数据流的性能。 若要打开数据流的执行计划和性能配置文件，请在管道的 "输出" 选项卡中单击 "操作" 下的眼镜图标。

![数据流监视器](media/data-flow/mon002.png "数据流监视器2")

 您可以使用此信息来估算针对不同大小的数据源的数据流的性能。 有关详细信息，请参阅[监视映射数据流](concepts-data-flow-monitoring.md)。

![数据流监视](media/data-flow/mon003.png "数据流监视器3")

 对于 "管道调试" 运行，在整体性能计算中大约一分钟的群集设置时间是热群集所必需的。 如果正在初始化默认 Azure Integration Runtime，则加速启动时间可能需要大约5分钟。

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>在 Azure Integration Runtime 中增加计算大小

具有更多核心的 Integration Runtime 增加了 Spark 计算环境中节点的数量，并提供更多的处理能力来读取、写入和转换数据。
* 如果希望处理速率高于输入速率，请尝试使用**计算优化**群集
* 如果要将更多数据缓存在内存中，请尝试使用**内存优化**群集。

![新 IR](media/data-flow/ir-new.png "新 IR")

有关如何创建 Integration Runtime 的详细信息，请参阅[Azure 数据工厂中的 Integration Runtime](concepts-integration-runtime.md)。

### <a name="increase-the-size-of-your-debug-cluster"></a>增加调试群集的大小

默认情况下，打开 "调试" 将使用为每个数据工厂自动创建的默认 Azure 集成运行时。 此默认 Azure IR 设置为八个核心，四个用于驱动程序节点，四个用于辅助节点，使用常规计算属性。 当你使用较大的数据进行测试时，你可以通过创建具有较大配置的 Azure IR，并在打开调试时选择此新 Azure IR 来增加调试群集的大小。 这会指示 ADF 使用此 Azure IR 进行数据预览，并使用数据流进行管道调试。

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>针对 Azure SQL 数据库和 Azure SQL 数据仓库进行优化

### <a name="partitioning-on-source"></a>对源进行分区

1. 中转到 "**优化**" 选项卡，然后选择 "**设置分区**"
1. 选择**源**。
1. 在 "**分区数**" 下，设置到 AZURE SQL DB 的最大连接数。 可以尝试使用更高的设置来获取与数据库的并行连接。 但是，在某些情况下，连接数可能会提高性能。
1. 选择是按特定表列还是按查询进行分区。
1. 如果选择了 "**列**"，请选取分区列。
1. 如果选择了 "**查询**"，请输入与数据库表的分区方案匹配的查询。 此查询允许源数据库引擎利用分区消除功能。 不需要对源数据库表进行分区。 如果源尚未分区，则 ADF 仍将根据你在源转换中选择的键在 Spark 转换环境中使用数据分区。

![源部分](media/data-flow/sourcepart3.png "源部分")

### <a name="source-batch-size-input-and-isolation-level"></a>源批大小、输入和隔离级别

在源转换的 "**源选项**" 下，下列设置可能会影响性能：

* 批大小指示 ADF 将数据存储在内存集中，而不是逐行保存。 批大小是一个可选设置，如果计算节点的大小不正确，则可能会用尽计算节点上的资源。
* 通过设置查询，可以在源中的行到达数据流进行处理之前对其进行筛选。 这可以使初始数据采集更快。 如果使用查询，可以为 Azure SQL DB 添加可选的查询提示，如 "未提交读"。
* 未提交读将提供更快的源转换查询结果

![源](media/data-flow/source4.png "Source")

### <a name="sink-batch-size"></a>接收器批大小

若要避免对数据流进行逐行处理，请在 Azure SQL 数据库和 Azure SQL DW 接收器的 "设置" 选项卡中设置**批大小**。 如果设置了批大小，则 ADF 会根据提供的大小处理批次数据库写入。

![接收器](media/data-flow/sink4.png "接收器")

### <a name="partitioning-on-sink"></a>接收器上的分区

即使你未将数据分区在目标表中，也建议在接收器转换中对数据进行分区。 通常，分区数据的加载速度更快，因为强制所有连接都使用单个节点/分区。 中转到接收器的 "优化" 选项卡，然后选择 "*轮循机制*分区"，选择要写入接收器的理想分区数。

### <a name="disable-indexes-on-write"></a>在写入时禁用索引

在管道中，将 "[存储过程" 活动](transform-data-using-stored-procedure.md)添加到 "数据流" 活动之前，该活动会在从接收器写入的目标表上禁用索引。 在 "数据流" 活动之后，添加另一个启用这些索引的存储过程活动。

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>增加 Azure SQL DB 和 DW 的大小

在管道运行之前，计划调整源的大小并接收器 Azure SQL 数据库和 DW，以提高吞吐量，并在达到 DTU 限制后将 Azure 限制降到最低。 管道执行完成后，重新调整数据库的大小，使其正常运行。

### <a name="azure-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[仅适用于 Azure SQL DW]通过 Polybase 使用过渡批量加载数据

若要避免逐行插入 DW，请选中 "在接收器设置中**启用过渡**"，以便 ADF 可以使用[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。 PolyBase 允许 ADF 大容量加载数据。
* 从管道执行数据流活动时，需要选择一个 Blob 或 ADLS Gen2 存储位置，以便在大容量加载期间暂存数据。

## <a name="optimizing-for-files"></a>优化文件

在每次转换时，可以在 "优化" 选项卡中设置希望数据工厂使用的分区方案。
* 对于较小的文件，您可能会发现，选择*单个分区*有时可以比要求 Spark 对小型文件进行分区更好、更快。
* 如果没有关于源数据的足够信息，请选择 "*轮循机制*分区" 并设置分区数。
* 如果你的数据具有可以是良好哈希键的列，请选择 "*哈希分区*"。

在数据预览和管道调试中进行调试时，基于文件的源数据集的限制和采样大小仅适用于返回的行数，而不是读取的行数。 这可能会影响调试执行的性能，并且可能会导致流失败。
* 默认情况下，调试群集是小型单节点群集，我们建议使用示例小文件进行调试。 使用临时文件，中转到 "调试设置" 并指向数据的一小部分。

    ![调试设置](media/data-flow/debugsettings3.png "调试设置")

### <a name="file-naming-options"></a>文件命名选项

在映射数据流写入 Blob 或 ADLS 文件存储时，最常用的方法是编写转换的数据。 在接收器中，你必须选择指向容器或文件夹的数据集，而不是指定的文件。 当映射数据流使用 Spark 执行时，会根据分区方案将输出拆分到多个文件中。

常见的分区方案是选择_输出到单个文件_，这会将所有输出部件文件合并到接收器中的单个文件中。 此操作要求输出减小到单个群集节点上的单个分区。 如果要将许多大的源文件合并到单个输出文件中，则可以用尽群集节点资源。

若要避免耗尽计算节点资源，请保留数据流中的默认优化方案，并在管道中添加一个复制活动，该活动将所有部分文件从输出文件夹合并到新的单个文件中。 此方法将转换操作与文件合并分隔开来，并获得与将输出设置_为单一文件_相同的结果。

### <a name="looping-through-file-lists"></a>遍历文件列表

当源转换循环访问多个文件而不是循环访问每个活动的时，映射数据流将更好地执行。 建议在源转换中使用通配符或文件列表。 通过允许在 Spark 群集内发生循环，可以更快地执行数据流进程。 有关详细信息，请参阅[源转换中的通配符](data-flow-source.md#file-based-source-options)。

例如，如果您的数据文件列表是您希望在 Blob 存储中的文件夹中处理的数据2019文件，下面是可以在源转换中使用的通配符。

```DateFiles/*_201907*.txt```

使用通配符时，管道将只包含一个数据流活动。 这比针对 Blob 存储进行查找更好，然后使用 ForEach 循环访问所有匹配文件，并在内部使用 "执行数据流" 活动。

## <a name="next-steps"></a>后续步骤

查看与性能相关的其他数据流文章：

- ["数据流优化" 选项卡](concepts-data-flow-overview.md#optimize)
- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
