---
title: Azure HDInsight 中的 Apache HBase 是什么？
description: 介绍 HDInsight 中的 Apache HBase - 在 Hadoop 上构建的 NoSQL 数据库。 了解相关用例并将 HBase 与其他 Hadoop 群集进行比较。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: afbf9aff09999a34a84d55634a868250fbb6d1ff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "82188954"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>什么是 Azure HDInsight 中的 Apache HBase

[Apache HBase](https://hbase.apache.org/) 是一种开源 NoSQL 数据库，它构建在 Apache Hadoop 的基础之上，并基于 [Google BigTable](https://cloud.google.com/bigtable/) 模型化。 HBase 针对无架构数据库中的大量数据提供随机访问和强一致性。 数据库按列系列进行组织。

从用户角度来看，HBase 类似于数据库。 数据存储在表的行和列中，行中的数据按列系列分组。 HBase 是一个无架构数据库。 列和数据类型可以不定义便使用。 开放源代码可进行线性伸缩，以处理上千节点上数 PB 的数据。 开放源代码可依赖 Hadoop 环境中的分布式应用程序提供的数据冗余、批处理以及其他功能。

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>如何在 Azure HDInsight 中实现 Apache HBase？

HDInsight HBase 以集成到 Azure 环境中的托管群集形式提供。 这些群集配置为在 [Azure 存储](./../hdinsight-hadoop-use-blob-storage.md)中直接存储数据，这样就减少了延迟，并提高了选择性能和价格的弹性。 此属性使客户能够构建用于处理大型数据集的交互式网站。 构建用于存储数百万个终结点的传感器和遥测数据的服务。 以及使用 Hadoop 作业分析这些数据。 对于 Azure 中的大数据项目，HBase 和 Hadoop 是不错的起点。 实时应用程序可以利用这些服务来处理大型数据集。

HDInsight 实现使用 HBase 的横向扩展体系结构自动提供表分片， 并提供读写强一致性和自动故障转移。 性能可通过对读取使用内存中缓存并对写入使用高吞吐量流式处理来提高。 可以在虚拟网络内部创建 HBase 群集。 有关详细信息，请参阅[在 Azure 虚拟网络上创建 HDInsight 群集](./apache-hbase-provision-vnet.md)。

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>如何在 HDInsight HBase 中管理数据？

数据可以在 HBase 中通过使用 HBase shell 中的 `create`、`get`、`put` 和 `scan` 命令来管理。 数据通过使用 `put` 写入到数据库，并通过使用 `get` 读取。 `scan` 命令用于从表中的多行获得数据。 Data 也可以使用 HBase C# API 进行管理，该 API 在 HBase REST API 顶部提供客户端库。 HBase 数据库还可以通过使用 [Apache Hive](https://hive.apache.org/) 进行查询。 有关这些编程模型的简介，请参阅[开始在 HDInsight 中将 Apache HBase 与 Apache Hadoop 配合使用](./apache-hbase-tutorial-get-started-linux.md)。 共同处理器也适用，这样，便可在托管数据库的节点中处理数据。

> [!NOTE]  
> Thrift 不受 HDInsight 中的 HBase 支持。

## <a name="use-cases-for-apache-hbase"></a>Apache HBase 用例

BigTable（以及延伸开来的 HBase）是从 Web 搜索创建的典型用例。 搜索引擎构建索引，将词语映射到包含这些词语的网页。 然而，还有 HBase 适用的许多其他用例，本部分中列出了其中几个。

|方案 |说明 |
|---|---|
|键值存储|HBase 可用作键-值存储，适合于管理消息系统。 Facebook 使用 HBase 作为其消息系统，它最适合于存储和管理 Internet 通信。 WebTable 使用 HBase 搜索和管理从网页中提取的表。|
|传感器数据|HBase 用于捕获从各种源逐步收集的数据。 这些数据包括社交分析和时序。 并使交互式仪表板保持显示最新趋势和计数器，以及管理审核日志系统。 具体示例包括：Bloomberg 交易终端以及开放时序数据库 (Open Time Series Database, OpenTSDB)。 OpenTSDB 存储所收集的服务器系统运行状况相关指标并提供了访问这些指标的方式。|
|实时查询|[Apache Phoenix](https://phoenix.apache.org/) 是 Apache HBase 的 SQL 查询引擎。 它以 JDBC 驱动程序的形式供用户访问，并且支持使用 SQL 来查询和管理 HBase 表。|
|HBase 即平台|应用程序可以将 HBase 作为数据存储库而在其上运行。 具体示例包括 Phoenix、OpenTSDB、`Kiji` 和 Titan。 应用程序也可以与 HBase 集成。 示例包括：[Apache Hive](https://hive.apache.org/)、Apache Pig、[Solr](https://lucene.apache.org/solr/)、Apache Storm、Apache Flume、[Apache Impala](https://impala.apache.org/)、Apache Spark、`Ganglia` 和 Apache Drill。|

## <a name="next-steps"></a>后续步骤

* [开始在 HDInsight 中将 Apache HBase 与 Apache Hadoop 配合使用](./apache-hbase-tutorial-get-started-linux.md)
* [在 Azure 虚拟网络上创建 HDInsight 群集](./apache-hbase-provision-vnet.md)
* [在 HDInsight 中配置 Apache HBase 复制](apache-hbase-replication.md)
