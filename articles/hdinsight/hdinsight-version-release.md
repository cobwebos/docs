---
title: HDInsight 4.0 概述（预览版）- Azure
description: 比较 HDInsight 3.6 与 HDInsight 4.0 的功能、限制和升级建议。
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: overview
ms.date: 10/04/2018
ms.openlocfilehash: 34582e66dec3b2f97efba7856ccfbf678f8f1f63
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408079"
---
# <a name="hdinsight-40-overview-preview"></a>HDInsight 4.0 概述（预览版）

Azure HDInsight 是 Azure 中最受企业客户青睐的开源 Apache Hadoop 和 Apache Spark 分析服务之一。 HDInsight (HDI) 4.0 是 [Hortonworks Data Platform (HDP) 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html) 提供的 Apache Hadoop 组件的云分发版。 本文提供有关最新 Azure HDInsight 版本以及如何升级的信息。

## <a name="whats-new-in-hdi-40"></a>HDI 4.0 中有哪些新增功能？

### <a name="apache-hive-30-and-llap"></a>Apache Hive 3.0 和 LLAP

Apache Hive 低延迟分析处理 (LLAP) 使用持久性查询服务器和内存中缓存，以提供有关远程云存储中数据的快速 SQL 查询结果。 Hive LLAP 利用一组执行的 Hive 查询片段的持久性守护程序。 LLAP 上的执行查询类似于没有 LLAP 的 Hive，其中工作人员任务在 LLAP 守护程序而不是容器内运行。

Hive LLAP 的优势包括：

* 可执行深入 SQL 分析，如复杂联接、子查询、开窗函数、排序、用户定义的函数和复杂聚合，且不会影响性能和可伸缩性。

* 可针对准备数据的同一存储中的数据进行交互式查询，无需将数据从存储移动到另一个引擎进行分析处理。

* 通过缓存查询结果，可重复使用以前计算的查询结果，从而节省了运行查询所需集群任务花费的时间和资源。

### <a name="hive-dynamic-materialized-views"></a>Hive 动态具体化视图

Hive 现支持动态具体化视图或相关摘要的预先计算，用于加速数据仓库中的查询处理。 具体化视图可本机存储在 Hive 中，并且可以无缝使用 LLAP 加速。

### <a name="hive-transactional-tables"></a>Hive 事务表

HDI 4.0 包括 Apache Hive 3，这需要驻留在 Hive 仓库中的事务表的原子性、一致性、隔离性和持久性 (ACID) 符合性。 符合 ACID 的表和表数据由 Hive 访问和管理。 创建、检索、更新和删除 (CRUD) 表中的数据必须采用优化行列 (ORC) 文件格式，但仅限插入的表支持所有文件格式。

* ACID v2 在存储格式和执行引擎方面均进行了性能提升。 

* 默认情况下 ACID 会启用，以允许对数据更新的全面支持。

* 借助改进的 ACID 功能，可在行级别更新和删除。

* 无性能开销。

* 无需 Bucket 存储。

* Spark 可通过 Hive 仓库连接器读取和写入 Hive ACID 表。

详细了解 [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html)。

### <a name="apache-spark"></a>Apache Spark

Apache Spark 使用 Hive 仓库连接器获取可更新的表和 ACID 事务。 借助单元仓库连接器，可将 Hive 事务表注册为 Spark 中的外部表，以访问完整事务功能。 先前版本仅支持表分区操作。 Hive 仓库连接器还支持流式处理 DataFrames，用于将读取和写入从 Spark 中流式传输到事务和流式处理 Hive 表中。

Spark 执行程序可直接连接到 Hive LLAP 守护程序，并通过事务方式检索和更新数据，从而允许 Hive 控制数据。

HDInsight 4.0 上的 Apache Spark 支持以下方案：

* 在用于报告的同一事务表上运行机器学习模型训练。
* 使用 ACID 事务安全地从 Spark ML 向 Hive 表添加列。
* 从 Hive 流式处理表对更改源运行 Spark 流式处理作业。
* 直接从 Spark 结构化流式处理作业创建 ORC 文件。

不必再担心意外尝试直接从 Spark 访问 Hive 事务表，从而导致结果不一致、数据重复或数据损坏。 在 HDI 4.0 中，Spark 表和 Hive 表保留在单独的元存储中。 使用 Hive 数据仓库连接器将 Hive 事务表显式注册为 Spark 外部表。

详细了解 [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html)。


### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 包含在 HDI 4.0 中，并进行了以下更改：

* Oozie 不再运行 Hive 操作。 已删除 Hive CLI 并替换为 BeeLine。

* 可通过在“job.properties”文件中包含排除模式，以从共享 lib 中排除不需要的依赖项。

详细了解 [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html)。

## <a name="how-to-upgrade-to-hdi-40"></a>如何升级到 HDI 4.0

对于任何主要版本，在生产环境中实现最新版本前，全面测试组件十分重要。 HDI 4.0 可供你开始升级过程，但 HDI 3.6 是默认选项，可防止意外事故。

从先前版本的 HDI 到 HDI 4.0 尚无支持的升级路径。 由于元存储和 blob 数据格式已更改，因此 HDI 4.0 与先前版本不兼容。 将新 HDI 4.0 环境与当前生产环境保持分离至关重要。 如果将 HDI 4.0 部署到当前环境，则元存储将升级，并且无法撤消。  

## <a name="limitations"></a>限制

* HDI 4.0 不支持 MapReduce。 改为使用 Apache Tez。 详细了解 [Apache Tez](https://tez.apache.org/)。

* HDI 4.0 中不再提供 Hive 视图。 

* Spark 和交互式查询群集不支持 Apache Zeppelin 中的 Shell 解释器。

* 无法在 Spark-LLAP 群集上*禁用* LLAP。 只能关闭 LLAP。

* Azure Data Lake Storage Gen2 无法在 Spark 群集中保存 Juypter Notebook。

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 文档](index.yml)
* [发行说明](hdinsight-release-notes.md)
