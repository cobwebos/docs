---
title: 映射数据流性能和调优指南
description: 了解影响 Azure 数据工厂中映射数据流性能的关键因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 1b3e3cd39edccd0952ce44d9cb050ca1d0c724ea
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605307"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>映射数据流性能和调优指南

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在 Azure 数据工厂中映射数据流提供了一个无代码接口，用于大规模设计、部署和协调数据转换。 如果您不熟悉映射数据流，请参阅[映射数据流概述](concepts-data-flow-overview.md)。

在设计和测试来自 ADF UX 的数据流时，请确保打开调试模式以实时执行数据流，而无需等待群集预热。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

此视频显示了一些使用数据流转换数据的示例计时：
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>监控数据流性能

在设计映射数据流时，可以通过单击配置面板中的数据预览选项卡来对每个转换进行单元测试。 验证逻辑后，将数据流作为管道中的活动端到端测试。 添加执行数据流活动并使用调试按钮测试数据流的性能。 要打开数据流的执行计划和性能配置文件，请单击管道输出选项卡中的"操作"下的眼镜图标。

![数据流监视器](media/data-flow/mon002.png "数据流监视器 2")

 您可以使用此信息来估计数据流针对不同大小的数据源的性能。 有关详细信息，请参阅[监视映射数据流](concepts-data-flow-monitoring.md)。

![数据流监控](media/data-flow/mon003.png "数据流监视器 3")

 对于管道调试运行，暖群集需要大约一分钟的群集设置时间。 如果要初始化默认 Azure 集成运行时，则启动时间可能需要大约 5 分钟。

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>在 Azure 集成运行时增加计算大小

具有更多内核的集成运行时会增加 Spark 计算环境中的节点数，并提供更多的处理能力来读取、写入和转换数据。 ADF 数据流利用 Spark 进行计算引擎。 Spark 环境非常适合内存优化的资源。
* 如果希望处理速率高于输入速率，请尝试**计算优化**群集。
* 如果要在内存中缓存更多数据，请尝试**内存优化**群集。 与计算优化相比，经过优化的内存每个核心的价格点更高，但可能会导致更快的转换速度。

![新红外](media/data-flow/ir-new.png "新红外")

有关如何创建集成运行时的详细信息，请参阅[Azure 数据工厂中的集成运行时](concepts-integration-runtime.md)。

### <a name="increase-the-size-of-your-debug-cluster"></a>增加调试群集的大小

默认情况下，打开调试将使用为每个数据工厂自动创建的默认 Azure 集成运行时。 此默认 Azure IR 设置为八个内核，4 个为驱动程序节点，4 个设置为辅助节点，使用常规计算属性。 使用较大数据进行测试时，可以通过创建具有较大配置的 Azure IR 来增加调试群集的大小，并在打开调试时选择此新的 Azure IR。 这将指示 ADF 使用此 Azure IR 进行数据预览和管道调试，并处理数据流。

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>使用 TTL 减少群集计算启动时间

数据流属性下的 Azure IR 中有一个属性，该属性允许您为工厂建立群集计算资源池。 使用此池，您可以按顺序提交数据流活动以执行。 建立池后，每个后续作业将需要 1-2 分钟，按需 Spark 群集执行作业。 资源池的初始设置大约需要 6 分钟。 指定您希望在生存时间 （TTL） 设置中维护资源池的时间量。

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>优化 Azure SQL 数据库和 Azure SQL 数据仓库突触

### <a name="partitioning-on-source"></a>在源上分区

1. 转到 **"优化**"选项卡并选择 **"设置分区"**
1. 选择**源**。
1. 在 **"分区数**"下，设置到 Azure SQL DB 的最大连接数。 您可以尝试较高的设置来获得与数据库的并行连接。 但是，在某些情况下，由于连接数量有限，可能会导致性能更快。
1. 选择是按特定表列还是按查询进行分区。
1. 如果选择 **"列"，** 则选取分区列。
1. 如果选择 **"查询"，** 请输入与数据库表的分区方案匹配的查询。 此查询允许源数据库引擎利用分区消除。 不需要对源数据库表进行分区。 如果源尚未分区，ADF 仍将根据在"源"转换中选择的键在 Spark 转换环境中使用数据分区。

![源部件](media/data-flow/sourcepart3.png "源部件")

> [!NOTE]
> 帮助您为源选择分区数的良好指南基于为 Azure 集成运行时设置的内核数，并将该数字乘以 5。 例如，如果要转换 ADLS 文件夹中的一系列文件，并且要使用 32 核 Azure IR，则目标分区数为 32 x 5 = 160 个分区。

### <a name="source-batch-size-input-and-isolation-level"></a>源批处理大小、输入和隔离级别

在源转换中的 **"源选项**"下，以下设置可能会影响性能：

* 批处理大小指示 ADF 将数据存储在 Spark 内存中的集中，而不是逐行存储数据。 批处理大小是一个可选设置，如果计算节点的大小不正确，则可能会耗尽资源。 不设置此属性将使用 Spark 缓存批处理默认值。
* 设置查询可以允许您在源的行到达数据流进行处理之前对其进行筛选。 这可以加快初始数据采集速度。 如果使用查询，则可以为 Azure SQL DB 添加可选的查询提示，例如"读取未提交"。
* 未提交读取将在源转换上提供更快的查询结果

![源](media/data-flow/source4.png "源")

### <a name="sink-batch-size"></a>沉分批大小

为避免逐行处理数据流，请在 Azure SQL DB 和 Azure SQL DW 接收器的"设置"选项卡中设置**批处理大小**。 如果设置了批处理大小，ADF 会根据提供的大小处理分批写入数据库。 不设置此属性将使用 Spark 缓存批处理默认值。

![接收器](media/data-flow/sink4.png "接收器")

### <a name="partitioning-on-sink"></a>在水槽上分区

即使您的目标表中没有分区数据，也建议在接收器转换中对数据进行分区。 分区数据通常会导致强制所有连接使用单个节点/分区的加载速度更快。 转到接收器的"优化"选项卡，然后选择 *"循环分区*"以选择要写入接收器的理想分区数。

### <a name="disable-indexes-on-write"></a>禁用写入时索引

在管道中，在数据流活动之前添加[存储过程活动](transform-data-using-stored-procedure.md)，该活动禁用从接收器写入的目标表上的索引。 在数据流活动之后，添加另一个启用这些索引的存储过程活动。 或者利用数据库接收器中的预处理和后处理脚本。

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>增加 Azure SQL DB 和 DW 的大小

在管道运行之前安排对源和接收器 Azure SQL DB 和 DW 的大小进行调整，以增加吞吐量并在达到 DTU 限制后最小化 Azure 限制。 管道执行完成后，将数据库调整到其正常运行速率。

* SQL DB 源表具有 887k 行和 74 列到 SQL DB 表，具有单个派生列转换，使用经过 80 核调试 Azure IR 的内存，端到端大约需要 3 分钟。

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[仅 Azure 同步 SQL DW]使用暂存通过聚基批量加载数据

若要避免逐行插入 DW，请在接收器设置中选中**启用暂存，** 以便 ADF 可以使用[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。 PolyBase 允许 ADF 批量加载数据。
* 当您从管道执行数据流活动时，您需要选择 Blob 或 ADLS Gen2 存储位置，以在批量加载期间暂存数据。

* 421Mb 文件的文件源包含 74 列到 Synapse 表，单个派生列转换使用经过优化的内存 80 核调试 Azure IR 端到端大约需要 4 分钟。

## <a name="optimizing-for-files"></a>优化文件

在每个转换中，您可以设置您希望数据工厂在"优化"选项卡中使用的分区方案。最好首先测试基于文件的接收器，保留默认分区和优化。

* 对于较小的文件，您可能会发现选择较少的分区有时比要求 Spark 对您的小文件进行分区更好、更快。
* 如果没有足够的源数据信息，请选择 *"循环*分区"并设置分区数。
* 如果数据具有可是好的哈希键的列，请选择*哈希分区*。

* 包含包含 74 列的 421Mb 文件的文件接收器的文件源和单个派生列转换使用经过优化的内存 80 核调试 Azure IR 端到端大约需要 2 分钟。

在数据预览和管道调试中调试时，基于文件的源数据集的限制和采样大小仅适用于返回的行数，而不是读取的行数。 这可能会影响调试执行的性能，并可能导致流失败。
* 默认情况下，调试群集是小型单节点群集，我们建议使用示例小文件进行调试。 转到调试设置，并使用临时文件指向数据的一小部分。

    ![调试设置](media/data-flow/debugsettings3.png "调试设置")

### <a name="file-naming-options"></a>文件命名选项

在映射写入 Blob 或 ADLS 文件存储的数据流中写入转换数据的最常见方法。 在接收器中，必须选择指向容器或文件夹的数据集，而不是命名文件。 当映射数据流使用 Spark 执行时，您的输出会根据您的分区方案拆分到多个文件上。

常见的分区方案是选择 _"输出到单个文件_"，该文件将所有输出 PART 文件合并到接收器中的单个文件中。 此操作要求将输出减少到单个群集节点上的单个分区。 如果要将许多大型源文件合并到单个输出文件中，则群集节点资源可能会耗尽。

为了避免耗尽计算节点资源，请将默认优化的方案保留在数据流中，并在管道中添加复制活动，将输出文件夹中的所有 PART 文件合并到新的单个文件。 此技术将转换操作与文件合并分开，并实现与将 _"输出"设置为单个文件_相同的结果。

### <a name="looping-through-file-lists"></a>循环访问文件列表

当源转换在多个文件上循环，而不是通过 For each 活动循环时，映射数据流将更好地执行。 我们建议在源转换中使用通配符或文件列表。 数据流过程将更快地执行，允许循环在 Spark 群集内进行。 有关详细信息，请参阅[源转换 中的通配符](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

例如，如果您有一个 2019 年 7 月的数据文件列表，您希望在 Blob 存储中的文件夹中处理，下面是可在源转换中使用通配符。

```DateFiles/*_201907*.txt```

通过使用通配符，管道将仅包含一个数据流活动。 这将比对 Blob 存储的查找效果更好，然后使用 ForEach 和内部执行数据流活动遍遍所有匹配的文件。

### <a name="optimizing-for-cosmosdb"></a>为宇宙发展优化

在 CosmosDB 接收器上设置吞吐量和批处理属性仅在从管道数据流活动执行该数据流期间生效。 在数据流执行后，CosmosDB 将遵守原始收集设置。

* 批处理大小：计算数据的粗排大小，并确保行大小 = 批处理大小小于 200 万。 如果是，则增加批处理大小以获得更好的吞吐量
* 吞吐量：在此处设置更高的吞吐量设置，以允许文档更快地写入 CosmosDB。 请记住，基于高吞吐量设置的 RU 成本较高。
*   写入吞吐量预算：使用小于每分钟总 R 机的值。 如果数据流具有大量 Spark 分区，则设置预算吞吐量将允许在这些分区之间实现更多平衡。

## <a name="join-performance"></a>加入性能

管理数据流中联接的性能是一项很常见的操作，您将在整个数据转换的整个生命周期中执行。 在 ADF 中，数据流不需要在联接之前对数据进行排序，因为这些操作在 Spark 中作为哈希联接执行。 但是，您可以通过"广播"联接优化提高性能。 这将通过将联接关系两侧的内容向下推送到 Spark 节点来避免随机播放。 这适用于用于参考查找的较小表。 可能不适合节点内存的较大表不适合广播优化。

另一个联接优化是构建联接，这样可以避免 Spark 实现交叉联接的倾向。 例如，当您在联接条件中包含文本值时，Spark 可能会将其视为首先执行完整点菜产品，然后筛选出联接值的要求。 但是，如果您确保联接条件两侧都有列值，则可以避免此 Spark 诱导的点菜产品并提高联接和数据流的性能。

## <a name="next-steps"></a>后续步骤

请参阅与性能相关的其他数据流文章：

- [数据流优化选项卡](concepts-data-flow-overview.md#optimize)
- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监控数据流性能](concepts-data-flow-monitoring.md)
