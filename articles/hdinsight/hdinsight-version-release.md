---
title: HDInsight 4.0 概述 - Azure
description: 比较 HDInsight 3.6 与 HDInsight 4.0 的功能、限制和升级建议。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 466c47492de9c2fc86f309f4cb74df1ff5411dcb
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929292"
---
# <a name="azure-hdinsight-40-overview"></a>Azure HDInsight 4.0 概述

Azure HDInsight 是最受 Apache Hadoop 和 Apache Spark 的企业客户青睐的服务之一。 HDInsight 4.0 是 Apache Hadoop 组件的云发行版。 本文提供有关最新 Azure HDInsight 版本以及如何升级的信息。

## <a name="whats-new-in-hdinsight-40"></a>HDInsight 4.0 中有哪些新功能？

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 与低延迟分析处理

Apache Hive 低延迟分析处理 (LLAP) 使用持久查询服务器和内存中缓存。 此过程针对远程云存储中的数据提供快速的 SQL 查询结果。 Hive LLAP 使用一组永久守护程序来执行 Hive 查询的片段。 LLAP 上的执行查询类似于没有 LLAP 的 Hive，其中工作人员任务在 LLAP 守护程序而不是容器内运行。

Hive LLAP 的优势包括：

* 能够在不牺牲性能和适应性的情况下进行深入的 SQL 分析。 例如，复杂联接、子查询、开窗函数、排序、用户定义函数和复杂聚合。

* 可针对准备数据的同一存储中的数据进行交互式查询，无需将数据从存储移动到另一个引擎进行分析处理。

* 可以通过缓存查询结果来重用以前计算的查询结果。 此缓存可节省运行查询所需的群集任务时所用的时间和资源。

### <a name="hive-dynamic-materialized-views"></a>Hive 动态具体化视图

Hive 现在支持动态具体化视图或相关摘要的预先计算。 这些视图可加速数据仓库中的查询处理。 具体化视图可本机存储在 Hive 中，并且可以无缝使用 LLAP 加速。

### <a name="hive-transactional-tables"></a>Hive 事务表

HDI 4.0 包括 Apache Hive 3。 Hive 3 要求驻留在 Hive 仓库中的事务表具有原子性、一致性、隔离性和持久合规性。 符合 ACID 的表和表数据由 Hive 访问和管理。 位于创建、检索、更新和删除 (CRUD) 表中的数据必须采用优化行列 (ORC) 文件格式， 但仅限插入的表支持所有文件格式。

* ACID v2 在存储格式和执行引擎方面均进行了性能提升。

* 默认情况下 ACID 会启用，以允许对数据更新的全面支持。

* 借助改进的 ACID 功能，可在行级别更新和删除。

* 无性能开销。

* 无需 Bucket 存储。

* Spark 可通过 Hive 仓库连接器读取和写入 Hive ACID 表。

详细了解 [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html)。

### <a name="apache-spark"></a>Apache Spark

Apache Spark 使用 Hive 仓库连接器获取可更新的表和 ACID 事务。 借助单元仓库连接器，可将 Hive 事务表注册为 Spark 中的外部表，以访问完整事务功能。 先前版本仅支持表分区操作。 Hive Warehouse Connector 还支持流式处理数据帧。  此过程从 Spark 的事务和流式处理 Hive 表流式读取数据并将数据流式写入其中。

Spark 执行程序可直接连接到 Hive LLAP 守护程序，并通过事务方式检索和更新数据，从而允许 Hive 控制数据。

HDInsight 4.0 上的 Apache Spark 支持以下方案：

* 在用于报告的同一事务表上运行机器学习模型训练。
* 使用 ACID 事务安全地从 Spark ML 向 Hive 表添加列。
* 从 Hive 流式处理表对更改源运行 Spark 流式处理作业。
* 直接从 Spark 结构化流式处理作业创建 ORC 文件。

不必再担心出现那种意外尝试直接从 Spark 访问 Hive 事务表的情况。 那样会导致结果不一致、数据重复或数据损坏。 在 HDInsight 4.0 中，Spark 表和 Hive 表保留在单独的元存储中。 使用 Hive 数据仓库连接器将 Hive 事务表显式注册为 Spark 外部表。

详细了解 [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html)。

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 包含在 HDI 4.0 中，并进行了以下更改：

* Oozie 不再运行 Hive 操作。 已删除 Hive CLI 并替换为 BeeLine。

* 可通过在“job.properties”文件中包含排除模式，以从共享 lib 中排除不需要的依赖项。

详细了解 [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html)。

## <a name="how-to-upgrade-to-hdinsight-40"></a>如何升级到 HDInsight 4.0

在生产环境中实施最新版本前，全面测试组件。 可以使用 HDInsight 4.0 来开始升级过程。 HDInsight 3.6 是防止意外出错的默认选项。

从先前版本的 HDInsight 到 HDInsight 4.0 尚无支持的升级路径。 由于元存储和 blob 数据格式已更改，因此 4.0 与先前版本不兼容。 将新的 HDInsight 4.0 环境与当前生产环境保持分开至关重要。 如果将 HDInsight 4.0 部署到当前环境，则元存储会被永久升级。  

## <a name="limitations"></a>限制

* HDInsight 4.0 不支持将 MapReduce 用于 Apache Hive。 改为使用 Apache Tez。 详细了解 [Apache Tez](https://tez.apache.org/)。
* HDInsight 4.0 不支持 Apache Storm。
* HDInsight 4.0 中不再提供 Hive 视图。
* Spark 和 Interactive Query 群集不支持 Apache Zeppelin 中的 Shell 解释器。
* 无法在 Spark-LLAP 群集上*禁用* LLAP。 只能关闭 LLAP。
* Azure Data Lake Storage Gen2 无法在 Spark 群集中保存 Juypter Notebook。
* 默认情况下，Apache Pig 在 Tez 上运行，但你可以将其更改为 Mapreduce
* 为了提高行和列安全性而推出的 Spark SQL Ranger 集成已弃用
* 由于在 HDInsight 4.0 中提供 Spark 2.4 和 Kafka 2.1，因此不再支持 Spark 2.3 和 Kafka 1.1。 建议在 HDInsight 4.0 中使用 Spark 2.4 和 Kafka 2.3 及更高版本。


## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 文档](index.yml)
* [发行说明](hdinsight-release-notes.md)
