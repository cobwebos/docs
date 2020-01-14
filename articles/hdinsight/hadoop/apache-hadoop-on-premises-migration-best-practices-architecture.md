---
title: 体系结构：本地 Apache Hadoop 到 Azure HDInsight
description: 了解有关将本地 Hadoop 群集迁移到 Azure HDInsight 的体系结构最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 2d0d5bb871612bc5e16a26eb49808c39661ffb50
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934689"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 体系结构最佳做法

本文提供有关 Azure HDInsight 系统体系结构的建议。 本文是帮助用户将本地 Apache Hadoop 系统迁移到 Azure HDInsight 的最佳做法系列教程中的其中一篇。

## <a name="use-multiple-workload-optimized-clusters"></a>使用多个工作负荷优化的群集

许多本地 Apache Hadoop 部署由支持多个工作负荷的单一大型群集构成。 此单一群集可能比较复杂，需要抑制单个服务的功能才能让各个组件配合工作。 将本地 Hadoop 群集迁移到 Azure HDInsight 需要在方法上面做出革新。

Azure HDInsight 群集是针对特定类型的计算用途设计的。 由于可以跨多个群集共享存储，因此可以创建多个工作负载优化的计算群集，以满足不同作业的需求。 每个群集类型根据该特定工作负荷采用最佳的配置。 下表列出了 HDInsight 中支持的群集类型和对应的工作负荷。

|工作负载|HDInsight 群集类型|
|---|---|
|批处理 (ETL/ELT)|Hadoop、Spark|
|数据仓库|Hadoop、Spark、交互式查询|
|IoT/流式处理|Kafka、Storm、Spark|
|NoSQL 事务处理|HBase|
|使用内存中缓存的更快交互式查询|交互查询|
|数据科学|ML Services、Spark|

下表显示了可用于创建 HDInsight 群集的各种方法。

|工具|基于浏览器|命令行|REST API|SDK|
|---|---|---|---|---|
|[Azure 门户](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure 数据工厂](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI（1.0 版）](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Azure 资源管理器模板](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

有关详细信息，请参阅 [HDInsight 中的群集类型](../hadoop/apache-hadoop-introduction.md)一文。

## <a name="use-transient-on-demand-clusters"></a>使用暂时性按需群集

HDInsight 群集可能长时间不被使用。 为了帮助节省资源成本，HDInsight 支持按需的暂时性群集，在成功完成工作负荷后，可以删除这些群集。

删除群集时，不会删除关联的存储帐户和外部元数据。 以后可以使用相同的存储帐户和元存储重新创建群集。

可以使用 Azure 数据工厂来计划按需 HDInsight 群集的创建。 有关详细信息，请参阅[使用 Azure 数据工厂在 HDInsight 中创建按需 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-adf.md)一文。

## <a name="decouple-storage-from-compute"></a>从计算资源解耦存储资源

典型的本地 Hadoop 部署使用相同的计算机组来存储和处理数据。 因为它们是协同定位的，所以必须将计算和存储一起横向扩展。

在 HDInsight 群集上，存储无需与计算协同定位，也可以在 Azure 存储中 Azure Data Lake Storage 或两者中。 从计算资源解耦存储资源可带来以下好处：

- 在群集之间共享数据。
- 由于数据不依赖于群集，因此可以使用暂时性群集。
- 降低存储成本。
- 单独缩放存储和计算资源。
- 跨区域复制数据。

在靠近 Azure 区域中存储帐户资源的位置创建群集，以消减隔离计算和存储资源所造成的性价比损失。 高速网络可让计算节点高效访问 Azure 存储中的数据。

## <a name="use-external-metadata-stores"></a>使用外部元数据存储

有两个适用于 HDInsight 群集的主要元存储： [Apache Hive](https://hive.apache.org/)和[Apache Oozie](https://oozie.apache.org/)。 Hive 元存储是 Hadoop、Spark、LLAP、Presto 和 Apache Pig 等数据处理引擎可以使用的中央架构存储库。 Oozie 元存储存储有关计划以及正在进行和已完成的 Hadoop 作业状态的详细信息。

HDInsight 对 Hive 和 Oozie 元存储使用 Azure SQL 数据库。 可通过两种方式在 HDInsight 群集中设置元存储：

1. 默认元存储

    - 没有任何额外费用。
    - 删除群集时会删除元存储。
    - 无法在不同的群集之间共享元存储。
    - 使用基本的 Azure SQL 数据库，DTU 限制为 5 个。

1. 自定义外部元存储

    - 将外部 Azure SQL 数据库指定为元存储。
    - 可以创建和删除群集，而不会丢失元数据，包括 Hive 架构 Oozie 作业详细信息。
    - 可与不同类型的群集共享单个元存储数据库。
    - 可根据需要纵向扩展元存储。
    - 有关详细信息，请参阅[在 Azure HDInsight 中使用外部元数据存储](../hdinsight-use-external-metadata-stores.md)。

## <a name="best-practices-for-hive-metastore"></a>Hive 元存储的最佳做法

下面是一些 HDInsight Hive 元存储最佳做法：

- 使用自定义外部元存储来隔离计算资源和元数据。
- 首先使用 S2 层 Azure SQL 实例，它提供 50 个 DTU 和 250 GB 存储空间。 如果空间不够，可扩大数据库。
- 不要将为一个 HDInsight 群集版本创建的元存储与不同版本的群集共享。 不同的 Hive 版本使用不同的架构。 例如，不能同时与 Hive 1.2 和 Hive 2.1 群集共享某个元存储。
- 定期备份自定义元存储。
- 将元存储和 HDInsight 群集保留在同一区域。
- 使用 Azure SQL 数据库监视工具（如 Azure 门户或 Azure Monitor 日志）监视元存储的性能和可用性。
- 根据需要执行 `ANALYZE TABLE` 命令，以便为表和列生成统计信息。 例如，`ANALYZE TABLE [table_name] COMPUTE STATISTICS` 。

## <a name="best-practices-for-different-workloads"></a>不同工作负荷的最佳做法

- 考虑对交互式 Hive 查询使用可改善响应时间的 LLAP 群集。[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP)  是 Hive 2.0 中的一项新功能，可用于在内存中缓存查询。 LLAP 能够大幅加快 Hive 查询的速度， [在某些情况下，速度比 Hive 1.x 要快 26 倍](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)。
- 考虑使用 Spark 作业取代 Hive 作业。
- 考虑使用 LLAP 查询取代基于 impala 的查询。
- 考虑使用 Spark 作业取代 MapReduce 作业。
- 考虑使用 Spark 结构化流作业取代低延迟 Spark 批处理作业。
- 考虑使用 Azure 数据工厂 (ADF) 2.0 来协调数据。
- 考虑使用 Ambari 进行群集管理。
- 将数据存储从本地 HDFS 更改为 WASB、ADLS 或 ADFS，以处理脚本。
- 考虑对 Hive 表和审核使用 Ranger RBAC。
- 考虑使用 CosmosDB 取代 MongoDB 或 Cassandra。

## <a name="next-steps"></a>后续步骤

阅读本系列教程的下一篇文章：

- [有关从本地迁移到 Azure HDInsight Hadoop 的基础结构最佳做法](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
