---
title: 使用 Ambari 优化 HDInsight 群集配置 - Azure | Microsoft Docs
description: 使用 Ambari Web UI 来配置和优化 HDInsight 群集。
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ashish
ms.openlocfilehash: f3c1edc767ab07bcdd8b09a0e40e291cbd1f3d9a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>使用 Ambari 优化 HDInsight 群集配置

HDInsight 为大规模数据处理应用程序提供 Apache Hadoop 群集。 对这些复杂的多节点群集进行管理、监视和优化可能存在一定的难度。 [Apache Ambari](http://ambari.apache.org/) 是可用于管理和监视 HDInsight Linux 群集的 Web 界面。  对于 Windows 群集，可以使用 Ambari [REST API](hdinsight-hadoop-manage-ambari-rest-api.md)。

有关使用 Ambari Web UI 的简介，请参阅[使用 Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)

使用群集凭据通过 `https://CLUSTERNAME.azurehdidnsight.net` 登录到 Ambari。 初始屏幕显示了概述仪表板。

![Ambari 仪表板](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Ambari Web UI 可用于管理主机、服务、警报、配置和视图。 Ambari 不可用于创建 HDInsight 群集、升级服务、管理堆栈和版本、停用或重用主机，或者将服务添加到群集。

## <a name="manage-your-clusters-configuration"></a>管理群集的配置

配置设置可帮助优化特定服务。 若要修改某个服务的配置设置，请从“服务”边栏（左侧）中选择该服务，然后在服务详细信息页中导航到“配置”选项卡。

![“服务”边栏](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>修改 NameNode Java 堆大小

NameNode Java 堆大小取决于许多因素，例如群集上的负载、文件数和块数。 默认大小 1 GB 能够很好地满足大多数群集的需要，不过，某些工作负荷可能需要更多或更少的内存。 

修改 NameNode Java 堆大小：

1. 从“服务”边栏中选择“HDFS”，然后导航到“配置”选项卡。

    ![HDFS 配置](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

2. 找到“NameNode Java 堆大小”设置。 也可以使用“筛选器”文本框键入和查找特定的设置。 选择设置名称旁边的**笔**图标。

    ![NameNode Java 堆大小](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

3. 在文本框中键入新值，然后按 **Enter** 保存更改。

    ![编辑 NameNode Java 堆大小](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

4. NameNode Java 堆大小已从 1GB 更改为 2 GB。

    ![已编辑 NameNode Java 堆大小](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

5. 单击配置屏幕顶部的绿色“保存”按钮保存所做的更改。

    ![保存更改](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Hive 优化

以下部分介绍了用于优化 Hive 总体性能的配置选项。

1. 若要修改 Hive 配置参数，请从“服务”边栏中选择“Hive”。
2. 导航到“配置”选项卡。

### <a name="set-the-hive-execution-engine"></a>设置 Hive 执行引擎

Hive 提供两个执行引擎：MapReduce 和 Tez。 Tez 的速度比 MapReduce 更快。 HDInsight Linux 群集将 Tez 用作默认的执行引擎。 更改执行引擎：

1. 在 Hive 的“配置”选项卡上的筛选框中，键入“执行引擎”。

    ![搜索执行引擎](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

2. “优化”属性的默认值为 **Tez**。

    ![优化 - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>优化映射器

Hadoop 会尝试将单个文件拆分（映射）为多个文件，以并行方式处理生成的文件。 映射器数目取决于拆分数目。 以下两个配置参数驱动 Tez 执行引擎的拆分数目：

* `tez.grouping.min-size`：分组拆分大小的下限，默认值为 16 MB（16,777,216 字节）。
* `tez.grouping.max-size`：分组拆分大小的上限，默认值为 1 GB（1,073,741,824 字节）。

在性能方面的一项经验法则是，减小这两个参数可以改善延迟，增大这两个参数可以提高吞吐量。

例如，若要为数据大小 128 MB 设置四个映射器任务，可将每个任务的这两个参数设置为 32 MB（33,554,432 字节）。

1. 若要修改限制参数，请导航到 Tez 服务的“配置”选项卡。 展开“常规”面板并找到 `tez.grouping.max-size` 和 `tez.grouping.min-size` 参数。

2. 将这两个参数设置为 **33,554,432** 字节 (32 MB)。

    ![Tez 分组大小](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
这些更改会影响整个服务器中的所有 Tez 作业。 若要获取最佳结果，请选择适当的参数值。

### <a name="tune-reducers"></a>优化化简器

ORC 和 Snappy 都提供高性能。 但是，Hive 默认提供的化简器可能太少，从而导致瓶颈。

例如，假设输入数据大小为 50 GB。 使用 Snappy 以 ORC 格式压缩这些数据后，大小为 1 GB。 Hive 估计所需的化简器数目为：(在映射器中输入的字节数 / `hive.exec.reducers.bytes.per.reducer`)。

如果使用默认设置，此示例的化简器数目为 4。

`hive.exec.reducers.bytes.per.reducer` 参数指定每个化简器处理的字节数。 默认值为 64 MB。 减小此值可提高并行度，并可能会改善性能。 但过度减小也可能生成过多的化简器，从而对性能产生潜在的负面影响。 此参数基于特定的数据要求、压缩设置和其他环境因素。

1. 若要修改该参数，请导航到 Hive 的“配置”选项卡，然后在“设置”页上找到“每个化简器的数据”参数。

    ![每个化简器的数据](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
2. 选择“编辑”并将该值修改为 128 MB（134,217,728 字节），然后按 **Enter** 保存。

    ![每个化简器的数据 - 已编辑](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    假设输入大小为 1024 MB，每个化简器的数据为 128 MB，则有 8 个化简器 (1024/128)。

3. 为“每个化简器的数据”参数提供错误的值可能导致生成大量的化简器，从而对查询性能产生负面影响。 若要限制化简器的最大数目，请将 `hive.exec.reducers.max` 设置为适当的值。 默认值为 1009。

### <a name="enable-parallel-execution"></a>启用并行执行

一个 Hive 查询是在一个或多个阶段中执行的。 如果可以并行运行各个独立阶段，则会提高查询性能。

1.  若要启用并行查询执行，请导航到 Hive 的“配置”选项卡并搜索 `hive.exec.parallel` 属性。 默认值为 false。 将该值更改为 true，然后按 **Enter** 保存该值。
 
2.  若要限制并行运行的作业数，请修改 `hive.exec.parallel.thread.number` 属性。 默认值为 8。

    ![Hive 并行执行](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>启用矢量化

Hive 逐行处理数据。 矢量化指示 Hive 以块（一个块包含 1,024 行）的方式处理数据，而不是以一次一行的方式处理数据。 矢量化只适用于 ORC 文件格式。

1. 若要启用矢量化查询执行，请导航到 Hive 的“配置”选项卡并搜索 `hive.vectorized.execution.enabled` 参数。 Hive 0.13.0 或更高版本的默认值为 true。
 
2. 若要为查询的化简端启用矢量化执行，请将 `hive.vectorized.execution.reduce.enabled` 参数设置为 true。 默认值为 false。

    ![Hive 矢量化执行](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>启用基于成本的优化 (CBO)

默认情况下，Hive 遵循一组规则来找到一个最佳的查询执行计划。 基于成本的优化 (CBO) 可以评估多个查询执行计划并向每个计划分配一个成本，然后确定成本最低的查询执行计划。

若要启用 CBO，请导航到 Hive 的“配置”选项卡并搜索 `parameter hive.cbo.enable`，然后将开关按钮切换到“开”。

![CBO 配置](./media/hdinsight-changing-configs-via-ambari/cbo.png)

启用 CBO 后，可使用以下附加配置参数提高 Hive 查询性能：

* `hive.compute.query.using.stats`

    如果设置为 true，则 Hive 会使用其元存储中存储的统计信息来应答类似于 `count(*)` 的简单查询。

    ![CBO 统计信息](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    启用 CBO 时，会创建列统计信息。 Hive 使用元存储中存储的列统计信息来优化查询。 如果列数较多，则提取每个列的列统计信息需要花费很长时间。 如果设置为 false，则会禁用从元存储中提取列统计信息。

    ![Hive 统计信息 - 设置列统计信息](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    行数、数据大小和文件大小等基本分区统计信息存储在元存储中。 如果设置为 true，则会从元存储中提取分区统计信息。 如果为 false，则从文件系统中提取文件大小，并从行架构中提取行数。

    ![Hive 统计信息 - 设置分区统计信息](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>启用中间压缩

映射任务将创建化简器任务使用的中间文件。 中间压缩可以缩小中间文件大小。

Hadoop 作业通常会遇到 I/O 瓶颈。 压缩数据能够加快 I/O 和总体网络传输速度。

可用的压缩类型包括：

| 格式 | 工具 | 算法 | 文件扩展名 | 是否可拆分？ |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .gz | 否 |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | 是 |
| LZO | Lzop | LZO | .lzo | 是（如果已编制索引） |
| Snappy | 不适用 | Snappy | Snappy | 否 |

一般规则是，尽量使用可拆分的压缩方法，否则会创建极少的映射器。 如果输入数据为文本，则 `bzip2` 是最佳选项。 对于 ORC 格式，Snappy 是最快的压缩选项。

1. 若要启用中间压缩，请导航到 Hive 的“配置”选项卡，并将 `hive.exec.compress.intermediate` 参数设置为 true。 默认值为 false。

    ![Hive 执行 - 中间压缩](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > 若要压缩中间文件，请选择一个 CPU 开销较低的压缩编解码器，即使该编解码器不能提供较高的压缩输出。

2. 若要设置中间压缩编解码器，请将自定义属性 `mapred.map.output.compression.codec` 添加到 `hive-site.xml` 或 `mapred-site.xml` 文件。

3. 添加自定义设置：

    a. 导航到 Hive 的“配置”选项卡并选择“高级”选项卡。

    b. 在“高级”选项卡下，找到并展开“自定义 hive-site”窗格。

    c. 单击“自定义 hive-site”窗格底部的“添加属性”链接。

    d. 在“添加属性”窗口中，输入 `mapred.map.output.compression.codec` 作为键，输入 `org.apache.hadoop.io.compress.SnappyCodec` 作为值。

    e. 单击 **“添加”**。

    ![Hive 自定义属性](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    这会使用 Snappy 压缩来压缩中间文件。 添加该属性后，它会显示在“自定义 hive-site”窗格中。

    > [!NOTE]
    > 此过程会修改 `$HADOOP_HOME/conf/hive-site.xml` 文件。

### <a name="compress-final-output"></a>压缩最终输出

还可以压缩最终的 Hive 输出。

1. 若要压缩最终的 Hive 输出，请导航到 Hive 的“配置”选项卡，并将 `hive.exec.compress.output` 参数设置为 true。 默认值为 false。

2. 若要选择输出压缩编解码器，请根据上一部分的步骤 3 所述，将 `mapred.output.compression.codec` 自定义属性添加到“自定义 hive-site”窗格。

    ![Hive 自定义属性](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>启用推理执行

推理执行可以启动特定数量的重复任务以检测运行速度缓慢的任务跟踪程序并将其加入方块列表，同时通过优化各项任务结果来改善总体作业执行。

不应该对输入量较大的长时间运行的 MapReduce 任务启用推理执行。

* 若要启用推理执行，请导航到 Hive 的“配置”选项卡，并将 `hive.mapred.reduce.tasks.speculative.execution` 参数设置为 true。 默认值为 false。

    ![Hive mapred 化简任务推理执行](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>优化动态分区

Hive 允许在表中插入记录时创建动态分区，且无需预定义每个分区。 这是一项强大功能，不管，它可能导致创建大量的分区并为每个分区创建大量的文件。

1. 要让 Hive 执行动态分区，应将 `hive.exec.dynamic.partition` 参数值设置为 true（默认值）。

2. 将动态分区模式更改为 *strict*。 在 strict（严格）模式下，必须至少有一个分区是静态的。 这可以阻止未在 WHERE 子句中包含分区筛选器的查询，即，*strict* 可阻止扫描所有分区的查询。 导航到 Hive 的“配置”选项卡，并将 `hive.exec.dynamic.partition.mode` 设置为 **strict**。 默认值为 **nonstrict**。
 
3. 若要限制要创建的动态分区数，请修改“hive.exec.max.dynamic.partitions”参数。 默认值为 5,000。
 
4. 若要限制每个节点的动态分区总数，请修改 `hive.exec.max.dynamic.partitions.pernode`。 默认值为 2,000。

### <a name="enable-local-mode"></a>启用本地模式

本地模式可让 Hive 在一台计算机上（有时是在单个进程中）执行某个作业的所有任务。 如果输入数据较小，并且查询启动任务的开销会消耗总体查询执行资源的绝大部分，则此模式可以提高查询性能。

若要启用本地模式，请根据[启用中间压缩](#enable-intermediate-compression)部分的步骤 3 所述，将 `hive.exec.mode.local.auto` 参数添加到“自定义 hive-site”面板。

![Hive 执行模式 - 本地自动](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>设置单个 MapReduce MultiGROUP BY

如果此属性设置为 true，则包含通用 group-by 键的 MultiGROUP BY 查询将生成单个 MapReduce 作业。  

若要启用此行为，请根据[启用中间压缩](#enable-intermediate-compression)部分的步骤 3 所述，将 `hive.multigroupby.singlereducer` 参数添加到“自定义 hive-site”窗格。

![在 Hive 中设置单个 MapReduce MultiGROUP BY](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>其他 Hive 优化

以下部分介绍了可以设置的其他 Hive 相关优化。

#### <a name="join-optimizations"></a>联接优化

Hive 中的默认联接类型是“随机联接”。 在 Hive 中，特殊的映射器会读取输入，并向中间文件发出联接键/值对。 Hadoop 在随机阶段中排序与合并这些对。 此随机阶段的系统开销较大。 根据数据选择右联接可以显著提高性能。

| 联接类型 | 时间 | 方式 | Hive 设置 | 注释 |
| -- | -- | -- | -- | -- |
| 随机联接 | <ul><li>默认选项</li><li>始终运行</li></ul> | <ul><li>从某个表的一部分内容中读取</li><li>根据联接键存储和排序</li><li>向每个化简器发送一个存储桶</li><li>在化简端执行联接</li></ul> | 不需要过多的 Hive 设置 | 每次运行 |
| 映射联接 | <ul><li>一个表可以装入内存</li></ul> | <ul><li>将小型表读入内存哈希表</li><li>通过大型文件的一部分流式处理</li><li>联接哈希表中的每条记录</li><li>只按映射器执行联接</li></ul> | `hive.auto.confvert.join=true` | 速度很快，但受限 |
| 排序合并存储桶 | 如果两个表： <ul><li>排序方式相同</li><li>存储方式相同</li><li>按排序/存储的列执行联接</li></ul> | 每个进程： <ul><li>从每个表中读取存储桶</li><li>处理值最小的行</li></ul> | `hive.auto.convert.sortmerge.join=true` | 非常高效 |

#### <a name="execution-engine-optimizations"></a>执行引擎优化

有关优化 Hive 执行引擎的其他建议：

| 设置 | 建议 | HDInsight 默认值 |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = 更安全，但速度更慢；false = 速度更快 | false |
| `tez.am.resource.memory.mb` | 大多数引擎的上限为 4 GB | 自动优化 |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>Pig 优化

可以通过 Ambari Web UI 修改 Pig 属性以优化 Pig 查询。 通过 Ambari 修改 Pig 属性会直接修改 `/etc/pig/2.4.2.0-258.0/pig.properties` 文件中的 Pig 属性。

1. 若要修改 Pig 属性，请导航到 Pig 的“配置”选项卡，然后展开“高级 pig-properties”窗格。

2. 查找、取消注释并更改相应的属性值。

3. 选择窗口右上方的“保存”以保存新值。 某些属性可能需要重启服务才能生效。

    ![高级 pig-properties](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> 任何会话级设置都会重写 `pig.properties` 文件中的属性值。

### <a name="tune-execution-engine"></a>优化执行引擎

可以使用两个执行引擎来执行 Pig 脚本：MapReduce 和 Tez。 Tez 是经过优化的引擎，比 MapReduce 要快得多。

1. 若要修改执行引擎，请在“高级 pig-properties”窗格中找到 `exectype` 属性。

2. 默认值为 **MapReduce**。 请将它更改为 **Tez**。


### <a name="enable-local-mode"></a>启用本地模式

与在 Hive 中一样，本地模式可用于加快作业，且生成的数据量相对较小。

1. 若要启用本地模式，请将 `pig.auto.local.enabled` 设置为 **true**。 默认值为 false。

2. 输入数据大小小于 `pig.auto.local.input.maxbytes` 属性值的作业被视为小型作业。 默认值为 1 GB。


### <a name="copy-user-jar-cache"></a>将用户 jar 复制到缓存中

Pig 可将 UDF 所需的 JAR 文件复制到分布式缓存，使这些文件可供任务节点使用。 这些 jar 不经常更改。 如果已启用，`pig.user.cache.enabled` 设置允许将 jar 放入缓存，使同一用户运行的作业能够重复使用这些文件。 这样可以小幅提高作业的性能。

1. 若要启用，请将 `pig.user.cache.enabled` 设置为 true。 默认值为 false。

2. 若要设置缓存 jar 的基本路径，请将 `pig.user.cache.location` 设置为基本路径。 默认为 `/tmp`。


### <a name="optimize-performance-with-memory-settings"></a>使用内存设置优化性能

以下内存设置可以帮助优化 Pig 脚本的性能。

* `pig.cachedbag.memusage`：分配给包的内存量。 包是元组的集合。 元组是字段的有序集，字段是数据片段。 如果包中的数据超过分配的内存，则会溢出到磁盘。 默认值为 0.2，表示可用内存的 20%。 这是在应用程序中的所有包之间分摊的内存量。

* `pig.spill.size.threshold`：超过此溢出大小阈值（以字节为单位）的包将溢出到磁盘。 默认值为 5 MB。


### <a name="compress-temporary-files"></a>压缩临时文件

Pig 在作业执行期间生成临时文件。 压缩临时文件可以在将文件读取或写入到磁盘时提高性能。 以下设置可用于压缩临时文件。

* `pig.tmpfilecompression`：如果为 true，则启用临时文件压缩。 默认值为 false。

* `pig.tmpfilecompression.codec`：用于压缩临时文件的压缩编解码器。 建议的压缩编解码器为 LZO 和 Snappy，它们可以较低 CPU 利用率。

### <a name="enable-split-combining"></a>启用拆分合并

如果已启用，则会合并小型文件，以减少映射任务数目。 这可以提高包含大量小型文件的作业的效率。 若要启用，请将 `pig.noSplitCombination` 设置为 true。 默认值为 false。


### <a name="tune-mappers"></a>优化映射器

可以通过修改 `pig.maxCombinedSplitSize` 属性来控制映射器数目。 此属性指定单个映射任务要处理的数据大小。 默认值为文件系统的默认块大小。 增大此值可减少映射器任务的数目。


### <a name="tune-reducers"></a>优化化简器

化简器数目根据 `pig.exec.reducers.bytes.per.reducer` 参数计算。 该参数指定每个化简器处理的字节数，默认值为 1 GB。 若要限制化简器的最大数目，请设置 `pig.exec.reducers.max` 属性，默认值为 999。


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>使用 Ambari Web UI 优化 HBase

可以通过 HBase 的“配置”选项卡修改 HBase 配置。以下部分介绍了一些影响 HBase 性能的重要配置设置。

### <a name="set-hbaseheapsize"></a>设置 HBASE_HEAPSIZE

HBase 堆大小指定区域服务器和主服务器要使用的最大堆数量（以 MB 为单位）。 默认值为 1,000 MB。 应该优化群集工作负荷的此项设置。

1. 若要修改，请导航到 HBase“配置”选项卡中的“高级 HBase-env”窗格，然后找到 `HBASE_HEAPSIZE` 设置。

2. 将默认值更改为 5,000 MB。

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>优化读取密集型工作负荷

以下配置对于提高读取密集型工作负荷的性能非常重要。

#### <a name="block-cache-size"></a>块缓存大小

块缓存是读取缓存。 其大小由 `hfile.block.cache.size` 参数控制。 默认值为 0.4，即总区域服务器内存的 40%。 块缓存大小越大，随机读取的速度越快。

1. 若要修改此参数，请导航到 HBase“配置”选项卡中的“设置”选项卡，然后找到“分配到读取缓冲区的 RegionServer 内存百分比”。

    ![HBase 块缓存大小](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
2. 若要更改此值，请选择“编辑”图标。


#### <a name="memstore-size"></a>Memstore 大小

所有编辑内容都存储在称作 *Memstore* 的内存缓冲区中。 此机制增大了可在单个操作中写入磁盘的总数据量，并可加速以后对最近编辑内容的访问。 Memstore 大小由以下两个参数定义：

* `hbase.regionserver.global.memstore.UpperLimit`：定义 Memstore 总共可以使用的区域服务器最大内存百分比。

* `hbase.regionserver.global.memstore.LowerLimit`：定义 Memstore 总共可以使用的区域服务器最小内存百分比。

若要优化随机读取，可以减小 Memstore 的上限和下限。


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>从磁盘扫描时提取的行数

`hbase.client.scanner.caching` 设置定义在扫描程序中调用 `next` 方法时，要从磁盘读取的行数。  默认值为 100。 该数字越大，从客户端向区域服务器发出的远程调用数就越少，因而扫描速度也就越快。 但是，这也会增大客户端上的内存压力。

![HBase 提取的行数](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> 设置此值时，请不要使扫描程序中的下一次方法调用间隔时间大于扫描程序的超时时间。 扫描程序超时期限由 `hbase.regionserver.lease.period` 属性定义。


### <a name="optimize-write-heavy-workloads"></a>优化写入密集型工作负荷

以下配置对于提高写入密集型工作负荷的性能非常重要。


#### <a name="maximum-region-file-size"></a>最大区域文件大小

HBase 使用称作 *HFile* 的内部文件格式存储数据。 属性 `hbase.hregion.max.filesize` 定义区域的单个 HFile 的大小。  如果区域中的 HFiles 总数大于此设置，则会将该区域拆分为两个区域。
 
![HBase HRegion 最大文件大小](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

区域文件大小越大，拆分数目越小。 可以增大文件大小，以确定可以最大程度地提高写入性能的值。


#### <a name="avoid-update-blocking"></a>避免阻止更新

* 属性 `hbase.hregion.memstore.flush.size` 定义 Memstore 刷新到磁盘的增量大小。 默认大小为 128 MB。

* Hbase 区域块乘数由 `hbase.hregion.memstore.block.multiplier` 定义。 默认值为 4。 允许的最大值为 8。

* 如果 Memstore 为 (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) 字节，则 HBase 会阻止更新。

    使用刷新大小和块乘数的默认值时，如果 Memstore 大小为 128 * 4 = 512 MB，则会阻止更新。 若要减少更新阻止计数，请增大 `hbase.hregion.memstore.block.multiplier` 的值。

![HBase 区域块乘数](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>定义 Memstore 大小

Memstore 大小由 `hbase.regionserver.global.memstore.UpperLimit` 和 `hbase.regionserver.global.memstore.LowerLimit` 参数定义。 将这些值设置为相等可以减少写入期间的暂停次数（同时提高刷新频率），并可以提高写入性能。


### <a name="set-memstore-local-allocation-buffer"></a>设置 Memstore 本地分配缓冲区

Memstore 本地分配缓冲区使用率由 `hbase.hregion.memstore.mslab.enabled` 属性确定。 如果已启用 (true)，则可以防止在执行写入密集型操作期间出现堆碎片。 默认值为 true。
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>后续步骤

* [使用 Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)
* [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
