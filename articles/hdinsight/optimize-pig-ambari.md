---
title: 在 Azure HDInsight 中通过 Apache Ambari 优化 Apache Pig
description: 使用 Apache Ambari web UI 配置和优化 Apache Pig。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 469019eb1e90654d1953156337593d5de99b46c0
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796676"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>在 Azure HDInsight 中通过 Apache Ambari 优化 Apache Pig

Apache Ambari 是一个 web 界面，用于管理和监视 HDInsight 群集。 有关 Ambari Web UI 的简介，请参阅[使用 Apache Ambari WEB Ui 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)。

可以通过 Ambari Web UI 修改 Apache Pig 属性以优化 Pig 查询。 通过 Ambari 修改 Pig 属性会直接修改 `/etc/pig/2.4.2.0-258.0/pig.properties` 文件中的 Pig 属性。

1. 若要修改 Pig 属性，请导航到 Pig 的“配置”选项卡，然后展开“高级 pig-properties”窗格。********

1. 查找、取消注释并更改相应的属性值。

1. 选择窗口右上角的 "**保存**" 以保存新值。 某些属性可能需要重启服务才能生效。

    ![高级 Apache pig 属性](./media/optimize-pig-ambari/advanced-pig-properties.png)

> [!NOTE]  
> 任何会话级设置都会重写 `pig.properties` 文件中的属性值。

## <a name="tune-execution-engine"></a>优化执行引擎

可以使用两个执行引擎来执行 Pig 脚本：MapReduce 和 Tez。 Tez 是经过优化的引擎，比 MapReduce 要快得多。

1. 若要修改执行引擎，请在“高级 pig-properties”窗格中找到 `exectype` 属性。****

1. 默认值为 **MapReduce**。 请将它更改为 **Tez**。

## <a name="enable-local-mode"></a>启用本地模式

与在 Hive 中一样，本地模式可用于加快作业，且生成的数据量相对较小。

1. 若要启用本地模式，请将 `pig.auto.local.enabled` 设置为 **true**。 默认值为 False。

1. 输入数据大小小于 `pig.auto.local.input.maxbytes` 属性值的作业被视为小型作业。 默认值为 1 GB。

## <a name="copy-user-jar-cache"></a>将用户 jar 复制到缓存中

Pig 可将 UDF 所需的 JAR 文件复制到分布式缓存，使这些文件可供任务节点使用。 这些 jar 不会频繁更改。 如果已启用，`pig.user.cache.enabled` 设置允许将 jar 放入缓存，使同一用户运行的作业能够重复使用这些文件。 此设置会导致作业性能的小幅增长。

1. 若要启用，请将 `pig.user.cache.enabled` 设置为 true。 默认值为 false。

1. 若要设置缓存 jar 的基本路径，请将 `pig.user.cache.location` 设置为基本路径。 默认值为 `/tmp`。

## <a name="optimize-performance-with-memory-settings"></a>使用内存设置优化性能

以下内存设置可以帮助优化 Pig 脚本的性能。

* `pig.cachedbag.memusage`：赋予袋的内存量。 包是元组的集合。 元组是字段的有序集，字段是数据片段。 如果袋中的数据超出了给定内存，则会将其溢出到磁盘。 默认值为 0.2，表示可用内存的 20%。 这是在应用程序中的所有包之间分摊的内存量。

* `pig.spill.size.threshold`：超过此溢出大小阈值（以字节为单位）的包将溢出到磁盘。 默认值为 5 MB。

## <a name="compress-temporary-files"></a>压缩临时文件

Pig 在作业执行期间生成临时文件。 压缩临时文件可以在将文件读取或写入到磁盘时提高性能。 以下设置可用于压缩临时文件。

* `pig.tmpfilecompression`：如果为 true，则启用临时文件压缩。 默认值为 False。

* `pig.tmpfilecompression.codec`：用于压缩临时文件的压缩编解码器。 建议的压缩编解码器是 LZO 和 Snappy，用于降低 CPU 使用率。

## <a name="enable-split-combining"></a>启用拆分合并

如果已启用，则会合并小型文件，以减少映射任务数目。 此设置可提高包含许多小文件的作业的效率。 若要启用，请将 `pig.noSplitCombination` 设置为 true。 默认值为 False。

## <a name="tune-mappers"></a>优化映射器

可以通过修改 `pig.maxCombinedSplitSize` 属性来控制映射器数目。 此属性指定单个映射任务要处理的数据的大小。 默认值为文件系统的默认块大小。 如果增大此值，映射器任务就会减少。

## <a name="tune-reducers"></a>优化化简器

化简器数目根据 `pig.exec.reducers.bytes.per.reducer` 参数计算。 该参数指定每个化简器处理的字节数，默认值为 1 GB。 若要限制化简器的最大数目，请`pig.exec.reducers.max`默认设置属性999。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [优化群集](./hdinsight-changing-configs-via-ambari.md)
* [优化 Apache HBase](./optimize-hbase-ambari.md)
* [优化 Apache Hive](./optimize-hive-ambari.md)
