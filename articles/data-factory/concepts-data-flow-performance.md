---
title: 映射数据流性能和优化指南
description: 了解影响 Azure 数据工厂中映射数据流性能的关键因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 07/06/2020
ms.openlocfilehash: 9f420b37bd44a46d4149e89cf5876d8e8b712581
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114374"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>映射数据流性能和优化指南

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂中的映射数据流提供一个无代码界面，用于大规模设计、部署和协调数据转换。 如果你不熟悉映射数据流，请参阅[映射数据流概述](concepts-data-flow-overview.md)。

在 ADF UX 中设计和测试数据流时，请确保打开调试模式来实时执行数据流，而无需等待群集预热。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

此视频演示了使用数据流转换数据的一些示例计时：
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>监视数据流性能

设计映射数据流时，可以通过单击配置面板中的数据预览选项卡对每个转换进行单元测试。 验证逻辑后，以管道中活动的形式对数据流进行端到端的测试。 添加“执行数据流”活动，并使用“调试”按钮测试数据流的性能。 若要打开数据流的执行计划和性能配置文件，请单击管道输出选项卡中“操作”下的眼镜图标。

![数据流监视器](media/data-flow/mon002.png "数据流监视器 2")

 可以使用此信息来评估数据流针对不同大小的数据源的性能。 有关详细信息，请参阅[监视映射数据流](concepts-data-flow-monitoring.md)。

![数据流监视](media/data-flow/mon003.png "数据流监视器 3")

 对于管道调试运行，大约需要占用总体性能计算结果中的大约 1 分钟群集设置时间来预热群集。 如果正在初始化默认 Azure Integration Runtime，则加速启动时间可能需要大约4分钟。

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>在 Azure Integration Runtime 中增大计算大小

核心更多的 Integration Runtime 可增加 Spark 计算环境中的节点数，并提供更强的处理能力来读取、写入和转换数据。 ADF 数据流对计算引擎使用 Spark。 Spark 环境可在内存优化资源中非常顺利地运行。

建议为生产工作负荷使用**优化的内存**。 你将能够将更多的数据存储在内存中，并将内存不足错误降到最低。 内存优化比计算优化，每个核心的价格更高，但可能会导致更快的转换速度和更成功的管道。 如果在执行数据流时遇到内存不足错误，请切换到内存优化的 Azure IR 配置。

**优化计算**可用于调试和数据预览数量有限的数据行。 优化计算可能不会与生产工作负荷同时执行。

![新 IR](media/data-flow/ir-new.png "新 IR")

有关如何创建 Integration Runtime 的详细信息，请参阅 [Azure 数据工厂中的 Integration Runtime](concepts-integration-runtime.md)。

### <a name="increase-the-size-of-your-debug-cluster"></a>增大调试群集的大小

默认情况下，打开调试会使用针对每个数据工厂自动创建的默认 Azure Integration Runtime。 此默认 Azure IR 设置为使用八个核心（四个核心用于驱动程序节点，四个核心用于工作器节点），并使用“常规计算”属性。 对较大的数据进行测试时，可以通过创建采用较大配置的 Azure IR 来增大调试群集的大小，并在打开调试时选择此新的 Azure IR。 这会指示 ADF 使用此 Azure IR 通过数据流进行数据预览和管道调试。

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>使用 TTL 减少群集计算启动时间

在 Azure IR 中，“数据流属性”下的某个属性可让你为工厂建立群集计算资源池。 使用此池可按顺序提交要执行的数据流活动。 建立池后，每个后续作业将花费 1-2 分钟时间，让按需 Spark 群集执行作业。 资源池的初始设置需要大约4分钟。 在生存时间 (TTL) 设置中指定你希望保留资源池的时间长短。

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>对 Azure SQL 数据库和 Azure SQL 数据仓库 Synapse 进行优化

### <a name="partitioning-on-source"></a>在源中分区

1. 转到“优化”选项卡并选择“设置分区” 
1. 选择“源”。
1. 在“分区数”下，设置与 Azure SQL 数据库建立的最大连接数。 可以尝试使用更高的设置来与数据库建立并行连接。 但在某些情况下，使用有限数目的连接可以提高性能。
1. 选择是按特定的表列还是按查询进行分区。
1. 如果选择了“列”，请选择分区列。
1. 如果选择了“查询”，请输入与数据库表的分区方案匹配的查询。 此查询允许源数据库引擎利用分区消除功能。 不需要将源数据库表分区。 如果源尚未分区，则 ADF 仍会基于你在源转换中选择的键，使用 Spark 转换环境中的数据分区。

![源部分](media/data-flow/sourcepart3.png "源部分")

> [!NOTE]
> 源分区数的合理指导值是为 Azure Integration Runtime 设置的核心数乘以 5。 例如，如果你要转换 ADLS 文件夹中的一系列文件，并且要利用 32 核心的 Azure IR，则目标分区数为 32 x 5 = 160 个分区。

### <a name="source-batch-size-input-and-isolation-level"></a>源批大小、输入和隔离级别

在源转换中的“源选项”下，以下设置可能会影响性能：

* 批大小指示 ADF 将数据存储在 Spark 内存的集内，而不是逐行存储数据。 批大小是一个可选设置。如果计算节点的大小不适当，则其上可能会出现资源耗尽的情况。 如果不设置此属性，则会利用 Spark 缓存批默认值。
* 通过设置查询，可以在源中的行抵达数据流进行处理之前筛选这些行。 这可以加快初始数据采集速度。 如果使用查询，可为 Azure SQL 数据库添加可选的查询提示，例如 READ UNCOMMITTED。
* Read Uncommitted 可在执行源转换时更快地提供查询结果

![数据源](media/data-flow/source4.png "源")

### <a name="sink-batch-size"></a>接收器批大小

若要避免逐行处理数据流，请在 Azure SQL 数据库和 Azure SQL 数据仓库接收器的“设置”选项卡中设置“批大小”。 如果设置了批大小，ADF 将会根据提供的大小分批处理数据库写入。 如果不设置此属性，则会利用 Spark 缓存批默认值。

![接收器](media/data-flow/sink4.png "接收器")

### <a name="partitioning-on-sink"></a>在接收器中分区

即使你尚未将目标表中的数据分区，我们也建议你将接收器转换中的数据分区。 数据分区通常可以大幅提高加载速度，因为这会强制所有连接使用单个节点/分区。 转到接收器的“优化”选项卡，然后选择“轮循机制”分区，以选择要写入到接收器的理想分区数。

### <a name="disable-indexes-on-write"></a>写入时禁用索引

在管道中的“数据流”活动前面添加一个[“存储过程”活动](transform-data-using-stored-procedure.md)，用于在自接收器写入的目标表中禁用索引。 在“数据流”活动的后面，添加另一个可启用这些索引的“存储过程”活动。 或者，在数据库接收器中利用处理前脚本和处理后脚本。

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>增大 Azure SQL 数据库和数据仓库的大小

在管道运行的前面，计划重设源和接收器 Azure SQL 数据库与数据仓库的大小，以提高吞吐量，并在达到 DTU 限制后尽量减小 Azure 限制。 管道执行完成后，根据数据库的正常运行速率重设其大小。

* 使用内存优化的 80 核心调试 Azure IR 将包含 88.7 万行和 74 列的 SQL 数据库源表转换成包含单个派生列的 SQL 数据库表，从前到后大约需要 3 分钟时间。

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[仅限 Azure Synapse SQL 数据仓库] 通过 Polybase 使用暂存来批量加载数据

若要避免逐行插入到数据仓库，请在接收器设置中选中“启用暂存”，使 ADF 能够使用 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。 PolyBase 允许 ADF 批量加载数据。
* 从管道执行数据流活动时，需要选择一个 Blob 或 ADLS Gen2 存储位置，用于在批量加载期间暂存数据。

* 使用内存优化的 80 核心调试 Azure IR 将包含 74 列的 421Mb 文件源转换成一个 Synapse 表和一个派生列，从前到后大约需要 4 分钟时间。

## <a name="optimizing-for-files"></a>对文件进行优化

每次转换时，都可以在“优化”选项卡中设置希望数据工厂使用的分区方案。良好的做法是先在保留默认分区和优化的情况下测试基于文件的接收器。 将分区设置为文件目标的接收器中的 "当前分区" 将允许 Spark 为工作负荷设置适当的默认分区。 默认分区使用每个分区128Mb。

* 对于较小的文件，你可能发现，选择更少的分区有时候比要求 Spark 将小型文件分区的效果更好且速度更快。
* 如果未获得有关源数据的足够信息，请选择“轮循机制”分区并设置分区数。
* 如果数据中包含可用作适当哈希键的列，请选择“哈希分区”。

* 使用内存优化的 80 核心调试 Azure IR 将包含 74 列的 421Mb 文件源转换成包含单个派生列的文件接收器，从前到后大约需要 2 分钟时间。

在数据预览和管道中调试时，基于文件的源数据集的限制和采样大小仅适用于返回的行数，而不适用于读取的行数。 这可能会影响调试执行性能，并可能会导致流失败。
* 默认情况下，调试群集是较小的单节点群集，我们建议使用较小的示例文件进行调试。 转到“调试设置”，并使用临时文件指向一小部分数据。

    ![调试设置](media/data-flow/debugsettings3.png "调试设置")

### <a name="file-naming-options"></a>文件命名选项

若要在映射数据流中写入转换后的数据，最常用方法是写入 Blob 或 ADLS 文件存储。 在接收器中，必须选择指向容器或文件夹（而不是命名文件）的数据集。 由于映射数据流使用 Spark 完成执行操作，因此会基于分区方案在多个文件中拆分输出。

常用的分区方案是选择“输出到单个文件”，这会将所有输出 PART 文件合并成接收器中的单个文件。 此操作要求输出缩减为单个群集节点上的单个分区。 如果将许多较大源文件合并成单个输出文件，可能会耗尽群集节点资源。

若要避免耗尽计算节点资源，请在数据流中保留已优化的默认方案，并在管道中添加一个复制活动，用于将输出文件夹中的所有 PART 文件合并到单个新文件中。 此方法将转换操作与文件合并操作隔离，其实现的结果与“输出到单一文件”设置相同。

### <a name="looping-through-file-lists"></a>循环访问文件列表

当源转换通过多个文件进行迭代而不是通过 For Each 活动进行循环时，映射数据流的执行效果更好。 我们建议在源转换中使用通配符或文件列表。 允许在 Spark 群集内部发生循环可以加快数据流进程的执行速度。 有关详细信息，请参阅[源转换中的通配符](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

例如，如果你要在 Blob 存储中的某个文件夹内处理 2019 年 7 月的数据文件列表，则可以在源转换中使用下面的通配符。

```DateFiles/*_201907*.txt```

使用通配符时，管道只包含一个数据流活动。 与针对 Blob 存储执行查找，然后使用包含“执行数据流”活动的 ForEach 来迭代所有匹配文件相比，使用通配符的性能更高。

处于并行模式的管道 For Each 针对每个已执行的数据流活动运转作业群集，因此会衍生多个群集。 这可能会导致 Azure 服务限制以及大量的并发执行。 但是，在管道中设置 Sequential 的情况下，在 For Each 内部使用“执行数据流”可以避免发生限制和资源耗尽。 这会强制数据工厂按顺序针对数据流执行每个文件。

如果按顺序对数据流使用 For Each，我们建议利用 Azure Integration Runtime 中的 TTL 设置。 这是因为，每个文件都将在迭代器内部产生一个完整的4分钟群集启动时间。

### <a name="optimizing-for-cosmosdb"></a>对 CosmosDB 进行优化

在 CosmosDB 接收器中设置的吞吐量和批属性只会在从管道数据流活动执行该数据流时生效。 执行数据流后，CosmosDB 会遵循原始集合设置。

* 批大小：计算数据的大致行大小，并确保行大小 * 批大小的结果小于 200 万。 如果小于 200 万，增大批大小可获得更好的吞吐量
* 吞吐量：在此处设置较高的吞吐量能够以更快的速度将文档写入 CosmosDB。 请记住，吞吐量设置高会提高 RU 成本。
*   写入吞吐量预算：使用小于每分钟 RU 总数的值。 如果某个数据流包含大量的 Spark 分区，则设置预算吞吐量可以在这些分区之间实现更好的平衡。

## <a name="join-and-lookup-performance"></a>联接和查找性能

管理数据流中的联接性能是一个很常见的操作，在整个数据转换生命周期内都要执行此操作。 在 ADF 中，数据流不要求在联接之前将数据排序，因为这些操作是在 Spark 中作为哈希联接执行的。 但是，使用适用于“联接”、“存在”和“查找”转换的“广播”联接优化可以获得性能改进的优势。

这会通过将联接关系的任何一端的内容下推到 Spark 节点，来避免即时随机排列。 对于用于引用查找的小型表，此方法非常合适。 无法装入节点内存的大型表不适合进行广播优化。

对于包含多个联接操作的数据流，推荐的配置是将 "自动" 设置为 "自动"，并使用***内存优化***的 Azure Integration Runtime 配置。 如果在执行数据流期间遇到内存不足错误或广播超时，可以关闭广播优化。 但是，这会导致数据流执行速度变慢。 （可选）可以指示数据流仅下推联接的左侧或右侧，或者同时下推两侧。

![广播设置](media/data-flow/newbroad.png "广播设置")

另一种联接优化是以某种方式生成联接，以避免 Spark 的实现交叉联接的倾向。 例如，在联接条件中包含文本值时，Spark 可能需要先执行完整笛卡尔积计算，然后筛选出联接的值。 但是，如果确保在联接条件的两侧都包含列值，则可以避免这种 Spark 引发的笛卡尔积，提高联接和数据流的性能。

## <a name="next-steps"></a>后续步骤

请参阅与性能相关的其他数据流文章：

- [数据流优化选项卡](concepts-data-flow-overview.md#optimize)
- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
