---
title: 在 Azure HDInsight 中通过 Apache Ambari 优化 Apache Hive
description: 使用 Apache Ambari web UI 来配置和优化 Apache Hive。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ce3916ef1155224a91c0736c3dabe907ae8d2611
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796364"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>在 Azure HDInsight 中通过 Apache Ambari 优化 Apache Hive

Apache Ambari 是一个 web 界面，用于管理和监视 HDInsight 群集。 有关 Ambari Web UI 的简介，请参阅[使用 Apache Ambari WEB Ui 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)。

以下部分介绍了用于优化 Apache Hive 总体性能的配置选项。

1. 若要修改 Hive 配置参数，请从“服务”边栏中选择“Hive”。****
1. 导航到“配置”选项卡。****

## <a name="set-the-hive-execution-engine"></a>设置 Hive 执行引擎

Hive 提供两个执行引擎： Apache Hadoop MapReduce 和 Apache TEZ。 Tez 的速度比 MapReduce 更快。 HDInsight Linux 群集将 Tez 用作默认的执行引擎。 更改执行引擎：

1. 在 Hive 的“配置”选项卡上的筛选框中，键入“执行引擎”。********

    ![Apache Ambari 搜索执行引擎](./media/optimize-hive-ambari/ambari-search-execution.png)

1. “优化”属性的默认值为 **Tez**。****

    ![优化-Apache Tez 引擎](./media/optimize-hive-ambari/optimization-apache-tez.png)

## <a name="tune-mappers"></a>优化映射器

Hadoop 会尝试将单个文件拆分（映射）为多个文件，以并行方式处理生成的文件。** 映射器数目取决于拆分数目。 以下两个配置参数驱动 Tez 执行引擎的拆分数目：

* `tez.grouping.min-size`：分组拆分大小的下限，默认值为 16 MB（16,777,216 字节）。
* `tez.grouping.max-size`：分组拆分大小的上限，默认值为 1 GB（1,073,741,824 字节）。

作为一项性能指导，请将这两个参数降到最低，提高延迟，提高吞吐量。

例如，若要为数据大小 128 MB 设置四个映射器任务，可将每个任务的这两个参数设置为 32 MB（33,554,432 字节）。

1. 若要修改限制参数，请导航到 Tez 服务的“配置”选项卡。**** 展开“常规”面板并找到 `tez.grouping.max-size` 和 `tez.grouping.min-size` 参数。****

1. 将这两个参数设置为 **33,554,432** 字节 (32 MB)。

    ![Apache Ambari Tez 分组大小](./media/optimize-hive-ambari/apache-tez-grouping-size.png)

这些更改会影响整个服务器中的所有 Tez 作业。 若要获取最佳结果，请选择适当的参数值。

## <a name="tune-reducers"></a>优化化简器

Apache ORC 和 Snappy 都提供高性能。 但是，Hive 默认提供的化简器可能太少，从而导致瓶颈。

例如，假设输入数据大小为 50 GB。 使用 Snappy 以 ORC 格式压缩这些数据后，大小为 1 GB。 Hive 估计所需的化简器数目为：(在映射器中输入的字节数 / `hive.exec.reducers.bytes.per.reducer`)。

对于默认设置，此示例为四个化简器。

`hive.exec.reducers.bytes.per.reducer` 参数指定每个化简器处理的字节数。 默认值为 64 MB。 减小此值可提高并行度，并可能会改善性能。 但过度减小也可能生成过多的化简器，从而对性能产生潜在的负面影响。 此参数基于特定的数据要求、压缩设置和其他环境因素。

1. 若要修改该参数，请导航到 Hive 的“配置”选项卡，然后在“设置”页上找到“每个化简器的数据”参数。********

    ![每个化简器的 Apache Ambari 数据](./media/optimize-hive-ambari/ambari-data-per-reducer.png)

1. 选择“编辑”并将该值修改为 128 MB（134,217,728 字节），然后按 **Enter** 保存。****

    ![每个化简器的 Ambari 数据-已编辑](./media/optimize-hive-ambari/data-per-reducer-edited.png)
  
    给定输入大小为 1024 MB，每个化简器 128 MB 数据，有8个化简器（1024/128）。

1. 为“每个化简器的数据”参数提供错误的值可能导致生成大量的化简器，从而对查询性能产生负面影响。**** 若要限制化简器的最大数目，请将 `hive.exec.reducers.max` 设置为适当的值。 默认值为 1009。

## <a name="enable-parallel-execution"></a>启用并行执行

一个 Hive 查询是在一个或多个阶段中执行的。 如果可以并行运行各个独立阶段，则会提高查询性能。

1. 若要启用并行查询执行，请导航到 Hive 的“配置”选项卡并搜索 `hive.exec.parallel` 属性。**** 默认值为 False。 将该值更改为 true，然后按 **Enter** 保存该值。

1. 若要限制并行运行的作业数，请修改 `hive.exec.parallel.thread.number` 属性。 默认值为 8。

    ![Apache Hive exec 并行显示](./media/optimize-hive-ambari/apache-hive-exec-parallel.png)

## <a name="enable-vectorization"></a>启用矢量化

Hive 逐行处理数据。 矢量化指示 Hive 以块（一个块包含 1,024 行）的方式处理数据，而不是以一次一行的方式处理数据。 矢量化只适用于 ORC 文件格式。

1. 若要启用矢量化查询执行，请导航到 Hive 的“配置”选项卡并搜索 `hive.vectorized.execution.enabled` 参数。**** Hive 0.13.0 或更高版本的默认值为 true。

1. 若要为查询的化简端启用矢量化执行，请将 `hive.vectorized.execution.reduce.enabled` 参数设置为 true。 默认值为 False。

    ![Apache Hive 向量化执行](./media/optimize-hive-ambari/hive-vectorized-execution.png)

## <a name="enable-cost-based-optimization-cbo"></a>启用基于成本的优化 (CBO)

默认情况下，Hive 遵循一组规则来找到一个最佳的查询执行计划。 基于成本的优化（CBO）计算多个计划以执行查询。 并为每个计划分配一项成本，并确定执行查询的最低价计划。

若要启用 CBO，请导航到**Hive 配置** > **Configs** > **设置**并查找 "**启用基于成本的优化器**"，然后将切换按钮切换到 **"打开**"。

![基于 HDInsight 开销的优化程序](./media/optimize-hive-ambari/hdinsight-cbo-config.png)

启用 CBO 后，可使用以下附加配置参数提高 Hive 查询性能：

* `hive.compute.query.using.stats`

    如果设置为 true，则 Hive 会使用其元存储中存储的统计信息来应答类似于 `count(*)` 的简单查询。

    ![使用统计信息 Apache Hive 计算查询](./media/optimize-hive-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    启用 CBO 时，会创建列统计信息。 Hive 使用元存储中存储的列统计信息来优化查询。 如果列数较多，则提取每个列的列统计信息需要花费很长时间。 如果设置为 false，则会禁用从元存储中提取列统计信息。

    ![Apache Hive 统计信息集列统计信息](./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    行数、数据大小和文件大小等基本分区统计信息存储在元存储中。 如果设置为 true，则从元存储中提取分区统计信息。 为 false 时，将从文件系统中提取文件大小。 行数和行数从行架构中提取出来。

    ![Hive 统计信息 - 设置分区统计信息](./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png)

## <a name="enable-intermediate-compression"></a>启用中间压缩

映射任务将创建化简器任务使用的中间文件。 中间压缩可以缩小中间文件大小。

Hadoop 作业通常会遇到 I/O 瓶颈。 压缩数据能够加快 I/O 和总体网络传输速度。

可用的压缩类型包括：

| 格式 | 工具 | 算法 | 文件扩展名 | 是否可拆分？ |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | DEFLATE | `.gz` | 否 |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | 是 |
| LZO | `Lzop` | LZO | `.lzo` | 是（如果已编制索引） |
| Snappy | 不可用 | Snappy | Snappy | 否 |

作为一般规则，可拆分的压缩方法非常重要，否则将创建很少的映射器。 如果输入数据为文本，则 `bzip2` 是最佳选项。 对于 ORC 格式，Snappy 是最快的压缩选项。

1. 若要启用中间压缩，请导航到 Hive 的“配置”选项卡，并将 `hive.exec.compress.intermediate` 参数设置为 true。**** 默认值为 False。

    !["Hive exec 压缩中间"](./media/optimize-hive-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > 若要压缩中间文件，请选择一个 CPU 开销较低的压缩编解码器，即使该编解码器不能提供较高的压缩输出。

1. 若要设置中间压缩编解码器，请将自定义属性 `mapred.map.output.compression.codec` 添加到 `hive-site.xml` 或 `mapred-site.xml` 文件。

1. 添加自定义设置：

    a. 导航到 " **Hive 配置** > **Configs** > " "**高级** > **自定义 Hive**" "站点"。

    b. 选择 "自定义 hive-站点" 窗格底部的 "**添加属性 ...** "。

    c. 在“添加属性”窗口中，输入 `mapred.map.output.compression.codec` 作为键，输入 `org.apache.hadoop.io.compress.SnappyCodec` 作为值。

    d. 选择“添加”  。

    !["Apache Hive 自定义属性添加"](./media/optimize-hive-ambari/hive-custom-property.png)

    此设置将使用 Snappy 压缩来压缩中间文件。 添加该属性后，它会显示在“自定义 hive-site”窗格中。

    > [!NOTE]  
    > 此过程会修改 `$HADOOP_HOME/conf/hive-site.xml` 文件。

## <a name="compress-final-output"></a>压缩最终输出

还可以压缩最终的 Hive 输出。

1. 若要压缩最终的 Hive 输出，请导航到 Hive 的“配置”选项卡，并将 `hive.exec.compress.output` 参数设置为 true。**** 默认值为 False。

1. 若要选择输出压缩编解码器，请根据上一部分的步骤 3 所述，将 `mapred.output.compression.codec` 自定义属性添加到“自定义 hive-site”窗格。

    ![Apache Hive 自定义属性 add2](./media/optimize-hive-ambari/hive-custom-property2.png)

## <a name="enable-speculative-execution"></a>启用推理执行

推理执行会启动一定数量的重复任务来检测和拒绝列出缓慢运行任务跟踪器。 同时通过优化各个任务结果来改善作业的整体执行。

不应该对输入量较大的长时间运行的 MapReduce 任务启用推理执行。

* 若要启用推理执行，请导航到 Hive 的“配置”选项卡，并将 `hive.mapred.reduce.tasks.speculative.execution` 参数设置为 true。**** 默认值为 False。

    ![' Hive mapred 缩短任务推理执行 '](./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

## <a name="tune-dynamic-partitions"></a>优化动态分区

Hive 允许在将记录插入到表中时创建动态分区，而无需预定义每个分区。 此功能是一项强大的功能。 尽管这可能会导致创建大量的分区。 每个分区有大量文件。

1. 要让 Hive 执行动态分区，应将 `hive.exec.dynamic.partition` 参数值设置为 true（默认值）。

1. 将动态分区模式更改为 *strict*。 在 strict（严格）模式下，必须至少有一个分区是静态的。 此设置可防止 WHERE 子句中包含分区筛选器的查询，即*严格*阻止扫描所有分区的查询。 导航到 Hive 的“配置”选项卡，并将 `hive.exec.dynamic.partition.mode` 设置为 **strict**。**** 默认值为 **nonstrict**。

1. 若要限制要创建的动态分区数，请修改 `hive.exec.max.dynamic.partitions` 参数。 默认值为 5000。

1. 若要限制每个节点的动态分区总数，请修改 `hive.exec.max.dynamic.partitions.pernode`。 默认值为 2000。

## <a name="enable-local-mode"></a>启用本地模式

本地模式使 Hive 可以在一台计算机上执行作业的所有任务。 或有时在单个进程中。 如果输入数据较小，此设置可提高查询性能。 而且，为查询启动任务所用的开销会消耗大量的总体查询执行。

若要启用本地模式，请根据[启用中间压缩](#enable-intermediate-compression)部分的步骤 3 所述，将 `hive.exec.mode.local.auto` 参数添加到“自定义 hive-site”面板。

![Apache Hive exec 模式本地自动](./media/optimize-hive-ambari/hive-exec-mode-local-auto.png)

## <a name="set-single-mapreduce-multigroup-by"></a>设置单个 MapReduce MultiGROUP BY

如果此属性设置为 true，则包含通用 group-by 键的 MultiGROUP BY 查询将生成单个 MapReduce 作业。  

若要启用此行为，请根据[启用中间压缩](#enable-intermediate-compression)部分的步骤 3 所述，将 `hive.multigroupby.singlereducer` 参数添加到“自定义 hive-site”窗格。

![在 Hive 中设置单个 MapReduce MultiGROUP BY](./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png)

## <a name="additional-hive-optimizations"></a>其他 Hive 优化

以下部分介绍了可以设置的其他 Hive 相关优化。

### <a name="join-optimizations"></a>联接优化

Hive 中的默认联接类型是“随机联接”。** 在 Hive 中，特殊的映射器会读取输入，并向中间文件发出联接键/值对。 Hadoop 在随机阶段中排序与合并这些对。 此随机阶段的系统开销较大。 根据数据选择右联接可以显著提高性能。

| 联接类型 | 时间 | 方式 | Hive 设置 | 注释 |
| --- | --- | --- | --- | --- |
| 随机联接 | <ul><li>默认选项</li><li>始终运行</li></ul> | <ul><li>从某个表的一部分内容中读取</li><li>根据联接键存储和排序</li><li>向每个化简器发送一个存储桶</li><li>在化简端执行联接</li></ul> | 不需要过多的 Hive 设置 | 每次运行 |
| 映射联接 | <ul><li>一个表可以装入内存</li></ul> | <ul><li>将小型表读入内存哈希表</li><li>通过大型文件的一部分流式处理</li><li>联接哈希表中的每条记录</li><li>只按映射器执行联接</li></ul> | `hive.auto.confvert.join=true` | 快速但有限 |
| 排序合并存储桶 | 如果两个表： <ul><li>排序方式相同</li><li>存储方式相同</li><li>按排序/存储的列执行联接</li></ul> | 每个进程： <ul><li>从每个表中读取存储桶</li><li>处理值最小的行</li></ul> | `hive.auto.convert.sortmerge.join=true` | 高效 |

### <a name="execution-engine-optimizations"></a>执行引擎优化

有关优化 Hive 执行引擎的其他建议：

| 设置 | 建议 | HDInsight 默认值 |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | True = 更安全，但速度更慢；false = 速度更快 | false |
| `tez.am.resource.memory.mb` | 最大大小为 4 GB 的上限 | 自动优化 |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="next-steps"></a>后续步骤

* [使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [优化 Azure HDInsight 中的 Apache Hive 查询](./hdinsight-hadoop-optimize-hive-query.md)
* [优化群集](./optimize-hive-ambari.md)
* [优化 Apache HBase](./optimize-hbase-ambari.md)
* [优化 Apache Pig](./optimize-pig-ambari.md)