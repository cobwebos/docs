---
title: "什么是 HDInsight、Hadoop 技术堆栈和群集？ - Azure | Microsoft Docs"
description: "介绍 HDInsight 以及 Hadoop 技术堆栈和组件，其中包括适用于大数据分析的 Spark、Kafka、Hive、HBase。"
keywords: "azure hadoop, hadoop azure, hadoop 简介, hadoop 技术堆栈, 什么是 hadoop 群集, hadoop 的用途"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: cgronlun
ms.openlocfilehash: e5ed09ddb1556e6c76813e71bcb31cf4f792b616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Azure HDInsight、Hadoop 技术堆栈和 Hadoop 群集简介
 本文介绍 Azure HDInsight，Hadoop 技术堆栈的云发行版， 同时还介绍什么是 Hadoop 群集，以及何时使用该群集。 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>什么是 HDInsight 和 Hadoop 技术堆栈？ 
Azure HDInsight 是 [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/) 提供的 Hadoop 组件的云发行版。 [Apache Hadoop](http://hadoop.apache.org/) 是原始的开源框架，适用于对计算机群集上的大数据集进行分布式处理和分析。 


Hadoop 技术堆栈包括相关的软件和实用程序（Apache Hive、HBase、Spark、Kafka 等）。 若要查看 HDInsight 上的可用 Hadoop 技术堆栈组件，请参阅[可以与 HDInsight 配合使用的组件和版本][component-versioning]。 若要详细了解 HDInsight 中的 Hadoop，请参阅 [HDInsight 的 Azure 功能页](https://azure.microsoft.com/services/hdinsight/)。

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>什么是 Hadoop 群集？何时使用它？
*Hadoop* 也是一种群集类型，其中包含：

* 适用于作业计划和资源管理的 YARN
* 适用于并行处理的 MapReduce
* Hadoop 分布式文件系统 (HDFS)
  
Hadoop 最常用于已存储数据的批处理。 HDInsight 中的其他种类的群集具有其他功能：Spark 因其速度更快的内存中处理功能而日益受人欢迎。 有关详细信息，请参阅 [HDInsight 上的群集类型](#overview)。

## <a name="what-is-big-data"></a>什么是大数据？
大数据描述任何大型的数字信息，例如：

* 工业设备提供的传感器数据
* 从网站收集的客户活动
* Twitter 新闻源

大数据的收集量在增加，收集速度在加快，收集格式在增多。 大数据可以是历史数据（即已存储的数据），也可以是实时数据（即从数据源流式传输的数据）。 

## <a name="overview"></a>HDInsight 中的群集类型
HDInsight 包括特定的群集类型和群集自定义功能，例如添加组件、实用程序和语言。

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark、Kafka、交互式查询、HBase、自定义以及其他群集类型
HDInsight 提供了以下群集类型：

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**：使用 [HDFS](#hdfs)、[YARN](#yarn) 资源管理和简单的 [MapReduce](#mapreduce) 编程模型并行处理和分析批处理数据。
* **[Apache Spark](http://spark.apache.org/)**：一种并行处理框架，支持内存中处理，提升大数据分析应用程序的性能；Spark 适用于 SQL、流式数据处理和机器学习。 请参阅[什么是 HDInsight 中的 Apache Spark？](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**：构建于 Hadoop 上的 NoSQL 数据库，用于为大量非结构化和半结构化数据（可能为数十亿行乘以数百万列）提供随机访问和高度一致性。 请参阅[什么是 HDInsight 上的 HBase？](hdinsight-hbase-overview.md)
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**：用于托管和管理并行分布式 R 进程的服务器。 它可让数据科研人员、统计人员和 R 程序员根据需要访问 HDInsight 上可缩放的分布式分析方法。 请参阅 [Overview of R Server on HDInsight](hdinsight-hadoop-r-server-overview.md)（R Server on HDInsight 概述）。
* **[Apache Storm](https://storm.incubator.apache.org/)**：分布式实时计算系统，用于快速处理大型数据流。 Storm 以 HDInsight 中的托管群集形式提供。 请参阅 [使用 Storm 和 Hadoop 分析实时传感器数据](hdinsight-storm-sensor-data-analysis.md)。
* **[Apache 交互式查询预览版（AKA：Live Long and Process）](https://cwiki.apache.org/confluence/display/Hive/LLAP)**：内存中缓存，用于实现交互式且更快的 Hive 查询。 请参阅[在 HDInsight 中使用交互式查询](hdinsight-hadoop-use-interactive-hive.md)。
* **[Apache Kafka](https://kafka.apache.org/)**：一种开源平台，用于生成流式处理的数据管道和应用程序。 Kafka 还提供了消息队列功能，允许用户发布和订阅数据流。 请参阅 [Apache Kafka on HDInsight 简介](hdinsight-apache-kafka-introduction.md)。

还可以使用以下方法配置群集：
* **[加入域的群集预览版](hdinsight-domain-joined-introduction.md)**：已加入 Active Directory 域的群集，因此可控制访问权限并提供对数据的管理。
* **[具有脚本操作的自定义集群](hdinsight-hadoop-customize-cluster-linux.md)**：脚本在预配期间运行且安装其他组件的群集。

### <a name="example-cluster-customization-scripts"></a>群集自定义脚本示例
脚本操作是在群集预配期间在 Linux 上面运行的 Bash 脚本，可用于在群集上安装其他组件。 

以下脚本示例由 HDInsight 团队提供：

* **[Hue](hdinsight-hadoop-hue-linux.md)**：一组 Web 应用程序，用来与群集交互。 仅 Linux 群集。
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)**：通过图形处理对事物或人员之间的关系建模。
* **[Solr](hdinsight-hadoop-solr-install-linux.md)**：一个企业级搜索平台，可对数据进行全文搜索。

有关开发自己的脚本操作的信息，请参阅[使用 HDInsight 进行脚本操作开发](hdinsight-hadoop-script-actions-linux.md)。

## <a name="components-and-utilities-on-hdinsight-clusters"></a>HDInsight 群集上的组件和实用程序
HDInsight 群集包含以下组件和实用程序：

* **[Ambari](#ambari)**：群集预配、管理、监视和实用程序。
* **[Avro](#avro)**（用于 Avro 的 Microsoft .NET 库）：Microsoft .NET 环境的数据序列化。 
* **[Hive 和 HCatalog](#hive)**：与 SQL 类似的查询，以及表和存储管理层。
* **[Mahout](#mahout)**：用于可缩放的机器学习应用程序。
* **[MapReduce](#mapreduce)**：用于 Hadoop 分布式处理和资源管理的旧框架。 请参阅 [YARN](#yarn)。
* **[Oozie](#oozie)**：工作流管理。
* **[Phoenix](#phoenix)**：基于 HBase 的关系型数据库层。
* **[Pig](#pig)**：更简单的 MapReduce 转换脚本。
* **[Sqoop](#sqoop)**：数据导入和导出。
* **[Tez](#tez)**：允许数据密集型进程大规模地高效运行。
* **[YARN](#yarn)**：资源管理，属于 Hadoop 核心库的一部分。
* **[ZooKeeper](#zookeeper)**：协调分布式系统中的进程。

> [!NOTE]
> 有关特定组件的信息和版本信息，请参阅 [HDInsight 中的 Hadoop 组件和版本][component-versioning]
>
>

### <a name="ambari"></a>Ambari
Apache Ambari 用于预配、管理和监视 Apache Hadoop 群集。 它包括一系列直观的操作员工具和一组免除 Hadoop 复杂性的可靠 API，可简化群集操作。 Linux 上的 HDInsight 群集提供 Ambari Web UI 和 Ambari REST API。 HDInsight 群集上的 Ambari 视图允许 UI 插件功能。
请参阅[使用 Ambari 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)和 <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API reference</a>（Apache Ambari API 参考）。

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)
Microsoft .NET Library for Avro 针对 Microsoft.NET 环境序列化实现了 Apache Avro 紧凑的二进制数据交换格式。 它定义一种与语言无关的架构，因此，以一种语言序列化的数据可以用另一种语言读取。 有关格式的详细信息可以在 <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro 规范</a>中找到。 Avro 文件的格式支持分布式 MapReduce 编程模型：文件是“可拆分的”，也就是说，可以搜寻文件中的任意点，然后从某一特定块开始读取。 若要了解相关方法，请参阅 [使用 Microsoft .NET Library for Avro 序列化数据](hdinsight-dotnet-avro-serialization.md)。 基于 Linux 的群集支持即将发布。

### <a name="hdfs"></a>HDFS
Hadoop 分布式文件系统 (HDFS) 是一种文件系统，采用 YARN 和 MapReduce，是 Hadoop 技术的核心。 它是 HDInsight 上 Hadoop 群集的标准文件系统。 请参阅[从 HDFS 兼容存储查询数据](hdinsight-hadoop-use-blob-storage.md)。

### <a name="hive"></a>Hive 和 HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> 是构建于 Hadoop 上的一个数据仓库软件，允许使用类似于 SQL 的语言（称为 HiveQL）来查询和管理分布式存储中的大型数据集。 Hive 与 Pig 一样，是基于 MapReduce 的抽象，可将查询转换为一系列 MapReduce 作业。 Hive 比 Pig 更接近于关系数据库管理系统，适用于结构化程度更高的数据。 对于非结构化数据，Pig 是更佳的选择。 请参阅[将 Hive 与 HDInsight 中的 Hadoop 配合使用](hdinsight-use-hive.md)。

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> 是 Hadoop 的表和存储管理层，为用户提供数据的关系视图。 在 HCatalog 中读取和写入文件时，可以采用任何适用于 Hive SerDe（序列化程序-反序列化程序）的格式。

### <a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> 是一个库，包含在 Hadoop 上运行的机器学习算法。 计算机学习应用程序采用统计学原理，使系统学习数据并使用以往的结果来确定将来的行为。 请参阅 [使用 Hadoop 上的 Mahout 生成电影推荐](hdinsight-mahout.md)。

### <a name="mapreduce"></a>MapReduce
MapReduce 是一个旧软件框架，用于编写并行批量处理大数据集的应用程序。 MapReduce 作业将分割大型数据集并将数据组织成键值对进行处理。 MapReduce 作业在 [YARN](#yarn) 上运行。 请参阅 Hadoop Wiki 中的 <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a>。

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> 是一个管理 Hadoop 作业的工作流协调系统。 它与 Hadoop 堆栈集成，支持 MapReduce、Pig、Hive 和 Sqoop 的 Hadoop 作业。 它也能用于安排特定于某系统的作业，例如 Java 程序或 shell 脚本。 请参阅 [Use Oozie with Hadoop](hdinsight-use-oozie-linux-mac.md)（将 Oozie 与 Hadoop 配合使用）。

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> 是基于 HBase 的关系数据库层。 Phoenix 提供 JDBC 驱动程序以允许用户直接查询和管理 SQL 表。 Phoenix 将查询和其他语句转换为本机 NoSQL API 调用（而不是使用 MapReduce），因此可以在 NoSQL 存储之上实现更快的应用程序。 请参阅[将 Apache Phoenix 和 SQuirreL 与 HBase 群集配合使用](hdinsight-hbase-phoenix-squirrel.md)。

### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> 是一个高级平台，可以使用一种简单脚本语言（称为 Pig Latin）对大型数据集执行复杂的 MapReduce 转换。 Pig 转换 Pig Latin 脚本，使其可以在 Hadoop 内运行。 可以创建用户定义的函数 (UDF) 来扩展 Pig Latin。 请参阅 [Use Pig with Hadoop](hdinsight-use-pig.md)（将 Pig 与 Hadoop 配合使用）。

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> 是一种工具，可以在 Hadoop 和关系数据库（如 SQL）或其他结构化数据存储之间尽可能高效地传输批量数据。 请参阅[将 Sqoop 与 Hadoop 配合使用](hdinsight-use-sqoop.md)。

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> 是一个构建于 Hadoop YARN 之上的应用程序框架，用于执行复杂的非循环常规图形数据处理。 它是 MapReduce 框架的更灵活、功能更强大的后继，允许数据密集型进程（如 Hive）更高效地大规模运行。 请参阅 [“使用 Hive 和 HiveQL”中的“使用 Apache Tez 提高性能”](hdinsight-use-hive.md#usetez)。

### <a name="yarn"></a>YARN
Apache YARN 是下一代 MapReduce（MapReduce 2.0 或 MRv2），它具有更大的可伸缩性和实时处理，支持超越 MapReduce 批处理的数据处理方案。 YARN 提供资源管理和分布式应用程序框架。 MapReduce 作业在 YARN 上运行。 请参阅 <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>（Apache Hadoop 下一代 MapReduce (YARN)）。

### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> 通过数据寄存器 (znode) 的共享层次结构命名空间协调大型分布式系统中的进程。 Znode 包含协调流程所需的少量元数据信息：状态、位置、配置，等等。 请参阅[带 HBase 群集的 ZooKeeper 和 Apache Phoenix](hdinsight-hbase-phoenix-squirrel-linux.md) 的示例。 

## <a name="programming-languages-on-hdinsight"></a>HDInsight 上的编程语言
HDInsight 群集 - Spark、HBase、Kafka、Hadoop 和其他群集 - 支持多种编程语言，但某些编程语言默认情况下未安装。 对于默认情况下未安装的库、模块或程序包，请[使用脚本操作来安装组件](hdinsight-hadoop-script-actions-linux.md)。 

### <a name="default-programming-language-support"></a>默认编程语言支持
默认情况下，HDInsight 群集支持：

* Java
* Python

可以使用[脚本操作](hdinsight-hadoop-script-actions-linux.md)来安装其他语言。

### <a name="java-virtual-machine-jvm-languages"></a>Java 虚拟机 (JVM) 语言
除了 Java 外，许多语言可以在 Java 虚拟机 (JVM) 上运行；但是，运行其中某些语言可能需要在群集上安装其他组件。

HDInsight 群集支持以下基于 JVM 的语言：

* Clojure
* Jython (Python for Java)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop 特定的语言
HDInsight 群集支持以下特定于 Hadoop 技术堆栈的语言：

* 用于 Pig 作业的 Pig Latin
* 用于 Hive 作业的 HiveQL 和 SparkSQL

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight 标准版和 HDInsight 高级版
HDInsight 提供两个类别的大数据云产品：标准和高级。 HDInsight 标准版提供企业级群集，组织可使用它运行其大数据工作负荷。 HDInsight 高级版基于标准功能生成，提供针对 HDInsight 群集的高级分析和安全功能。 有关详细信息，请参阅 [Azure HDInsight 高级版](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="microsoft-business-intelligence-and-hdinsight"></a>Microsoft 商业智能和 HDInsight
大家熟悉的商业智能 (BI) 工具使用 Power Query 外接程序或 Microsoft Hive ODBC 驱动程序来检索、分析和报告与 HDInsight 集成的数据：

* [使用 Power Query 将 Excel 连接到 Hadoop](hdinsight-connect-excel-power-query.md)：了解如何使用 Microsoft Power Query for Excel，将 Excel 连接到存储 HDInsight 群集数据的 Azure 存储帐户。 所需的 Windows 工作站。 
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)：了解如何使用 Microsoft Hive ODBC 驱动程序从 HDInsight 导入数据。 所需的 Windows 工作站。 
* [Microsoft 云平台](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx)：了解有关 Power BI for Office 365、下载 SQL Server 试用版，以及设置 SharePoint Server 2013 和 SQL Server BI 的信息。
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>后续步骤

* [Get started with Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)（HDInsight 中的 Hadoop 入门）：有关预配 HDInsight Hadoop 群集和运行示例 Hive 查询的快速入门教程。
* [HDInsight 中的 Spark 入门](hdinsight-apache-spark-jupyter-spark-sql.md)：有关创建 Spark 群集和运行交互式 Spark SQL 查询的快速入门教程。
* [在 HDInsight 上使用 R Server](hdinsight-hadoop-r-server-get-started.md)：开始在 HDInsight 高级版中使用 R Server。
* [Provision HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md)（预配 HDInsight 群集）：了解如何通过 Azure 门户、Azure CLI 或 Azure PowerShell 预配 HDInsight Hadoop 群集。


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/