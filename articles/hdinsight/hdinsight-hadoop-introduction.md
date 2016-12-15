---
title: "Hadoop 简介 - HDInsight 上的 Hadoop 是什么？ | Microsoft Docs"
description: "获取有关 Hadoop 的简介、分布式大数据处理和分析，以及云中 HDInsight 上的 Hadoop 生态系统组件的简介。"
keywords: "大数据分析,hadoop 简介,什么是 hadoop,hadoop 技术堆栈,hadoop 生态系统"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/21/2016
ms.author: cgronlun
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 66200ad8688928c368146a177130f6e2fad4849b


---
# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Azure HDInsight 上的 Hadoop 生态系统简介
 本文介绍 Azure HDInsight 上的 Hadoop 及其生态系统，另外还介绍了大数据。 了解 Hadoop 组件、常用术语和大数据分析方案。

## <a name="what-is-hadoop-on-hdinsight"></a>HDInsight 上的 Hadoop 是什么？
Hadoop 是一种开源软件生态系统，在计算机群集上充当大数据的分布式处理、存储和分析框架。 借助 Azure HDInsight，可以在云中使用 **Hortonworks 数据平台 (HDP)** 分发版中的 Hadoop 组件，并且可以部署和预配具有高可靠性与可用性的托管群集。  

Apache Hadoop 是用于大数据处理的原始开源项目。 在此基础上，可以开发被视为 Hadoop 技术堆栈一部分的相关软件和实用工具，包括 Apache Hive、Apache HBase、Apache Spark，等等。 有关详细信息，请参阅 [HDInsight 中的 Hadoop 生态系统概述](#overview)。

## <a name="what-is-big-data"></a>什么是大数据？
大数据描述各种大型数字信息，从 Twitter 源中的文本到工业设备的传感器信息，再到客户在网站上进行浏览和购买的信息。 大数据可以是历史数据（即已存储的数据），也可以是实时数据（即从数据源直接流式传输的数据）。 大数据的收集量日益增加，收集速度越来越快，收集格式也越来越多。

若要使大数据成为可付诸行动的情报或见解，必须收集相关的数据并提出正确的问题。 另外，必须确保这些数据可供访问、清理和分析，然后以有效的方式呈现。 这就是有关 HDInsight 中的 Hadoop 的大数据分析的有用之处。

## <a name="a-nameoverviewaoverview-of-the-hadoop-ecosystem-in-hdinsight"></a><a name="overview"></a>HDInsight 中的 Hadoop 生态系统概述
HDInsight 是快速发展中的 Apache Hadoop 技术堆栈在 Microsoft Azure 上的云分发版，用于大数据分析。 它包括 Apache Spark、HBase、Storm、Pig、Hive、Sqoop、Oozie、Ambari 等的实现。 HDInsight 还可集成商业智能 (BI) 工具，例如 Power BI、Excel、SQL Server Analysis Services 和 SQL Server Reporting Services。

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop、HBase、Spark、Storm 和自定义群集
HDInsight 为 Apache Hadoop、Spark、HBase 或 Storm 提供群集配置。 或者，你可以 [使用脚本操作自定义群集](hdinsight-hadoop-customize-cluster-linux.md)。

* **Hadoop**：提供可靠的 [HDFS](#hdfs) 数据存储和一个简单的 [MapReduce](#mapreduce) 编程模型用于并行处理和分析数据。
* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>**：一种并行处理框架，支持内存中处理，提升大数据分析应用程序的性能；Spark 适用于 SQL、流式数据处理和机器学习。 请参阅 [概述：什么是 HDInsight 中的 Apache Spark？](hdinsight-apache-spark-overview.md)
* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**：基于 Hadoop 上的 NoSQL 数据库构建，为大量非结构化和半结构化数据（可能为数十亿行乘以数百万列）提供随机访问和高度一致性。 请参阅 [HDInsight 中的 HBase 概述](hdinsight-hbase-overview.md)。
* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>**：分布式实时计算系统，用于快速处理大型数据流。 Storm 以 HDInsight 中的托管群集形式提供。 请参阅 [使用 Storm 和 Hadoop 分析实时传感器数据](hdinsight-storm-sensor-data-analysis.md)。

### <a name="example-customization-scripts"></a>自定义脚本示例
脚本操作是在群集预配期间运行的脚本，可用于在群集上安装其他组件。 对于基于 Linux 群集，这些都是 Bash 脚本。

以下脚本示例由 HDInsight 团队提供：

* [Hue](hdinsight-hadoop-hue-linux.md)：一组 Web 应用程序，用来与群集交互。 仅 Linux 群集。
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)：通过图形处理对事物或人员之间的关系建模。
* [R](hdinsight-hadoop-r-scripts-linux.md)：一种开放源代码语言与环境，可在机器学习中用于统计计算。
* [Solr](hdinsight-hadoop-solr-install-linux.md)：一种企业级搜索平台，可对数据进行全文搜索。

有关开发你自己的脚本操作的信息，请参阅 [使用 HDInsight 进行脚本操作开发](hdinsight-hadoop-script-actions-linux.md)。

## <a name="what-are-the-hadoop-components-and-utilities"></a>有哪些 Hadoop 组件和实用工具？
HDInsight 群集包含以下组件和实用程序。

* **[Ambari](#ambari)**：群集预配、管理、监视和实用程序。
* **[Avro](#avro)**（用于 Avro 的 Microsoft .NET 库）：Microsoft .NET 环境的数据序列化。
* **[Hive 和 HCatalog](#hive)**：与结构化查询语言 (SQL) 类似的查询，以及表和存储管理层。
* **[Mahout](#mahout)**：用于可缩放的机器学习应用程序。
* **[MapReduce](#mapreduce)**：用于 Hadoop 分布式处理和资源管理的旧框架。 请参阅 [YARN](#yarn)，它是下一代资源框架。
* **[Oozie](#oozie)**：工作流管理。
* **[Phoenix](#phoenix)**：基于 HBase 的关系数据库层。
* **[Pig](#pig)**：更简单的 MapReduce 转换脚本。
* **[Sqoop](#sqoop)**：数据导入和导出。
* **[Tez](#tez)**：允许数据密集型进程大规模地高效运行。
* **[YARN](#yarn)**：Hadoop 核心库和下一代 MapReduce 软件框架的一部分。
* **[ZooKeeper](#zookeeper)**：协调分布式系统中的进程。

> [!NOTE]
> 有关特定组件的信息和版本信息，请参阅 [HDInsight 中的 Hadoop 组件、版本和服务产品][component-versioning]
> 
> 

### <a name="a-nameambariaambari"></a><a name="ambari"></a>Ambari
Apache Ambari 用于设置、管理和监视 Apache Hadoop 群集。 它包括一系列直观的操作员工具和一组免除 Hadoop 复杂性的可靠 API，可简化群集操作。 基于 Linux 的 HDInsight 群集提供 Ambari Web UI 和 Ambari REST API，而基于 Windows 的群集提供 REST API 的子集。 HDInsight 群集上的 Ambari 视图允许 UI 插件功能。

请参阅[使用 Ambari 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)（仅限 Linux）、[使用 Ambari API 监视 HDInsight 中的 Hadoop 群集](hdinsight-monitor-use-ambari-api.md)和 <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API 参考</a>。

### <a name="a-nameavroaavro-microsoft-net-library-for-avro"></a><a name="avro"></a>Avro (Microsoft .NET Library for Avro)
Microsoft .NET Library for Avro 针对 Microsoft.NET 环境序列化实现了 Apache Avro 紧凑的二进制数据交换格式。 它使用 <a target="_blank" href="http://www.json.org/">JavaScript 对象表示法 (JSON)</a> 定义与语言无关的架构，以支持语言互操作性，这意味着以一种语言序列化的数据可以用另一种语言读取。 有关格式的详细信息可以在 <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro 规范</a>中找到。
Avro 文件格式支持分布式 MapReduce 编程模型。 文件是“可拆分的”，也就是说，你可以在文件中任意设置一个点，然后即可从某一特定块开始读取。 若要了解相关方法，请参阅 [使用 Microsoft .NET Library for Avro 序列化数据](hdinsight-dotnet-avro-serialization.md)。

### <a name="a-namehdfsahdfs"></a><a name="hdfs"></a>HDFS
Hadoop 分布式文件系统 (HDFS) 是一种分布式文件系统，采用 MapReduce 和 YARN，是 Hadoop 生态系统的核心。 HDFS 是 HDInsight 上 Hadoop 群集的标准文件系统。

### <a name="a-namehiveahive-hcatalog"></a><a name="hive"></a>Hive 和 HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> 是构建于 Hadoop 上的一个数据仓库软件，允许使用类似于 SQL 的语言（称为 HiveQL）来查询和管理分布式存储中的大型数据集。 Hive（类似于 Pig）是一种基于 MapReduce 的抽象。 在运行时，Hive 会将查询转换为一系列 MapReduce 作业。 Hive 比 Pig 在概念上更接近于关系数据库管理系统，因此适用于结构化程度更高的数据。 对于非结构化数据，Pig 是更佳的选择。 请参阅 [将 Hive 与 HDInsight 中的 Hadoop 配合使用](hdinsight-use-hive.md)。

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> 是 Hadoop 的表和存储管理层，为用户提供数据的关系视图。 在 HCatalog 中，你可以读取和写入采用可以写入 Hive SerDe（序列化程序-反序列化程序）的任何格式的文件。

### <a name="a-namemahoutamahout"></a><a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> 是在 Hadoop 上运行的一种可缩放的机器学习算法库。 计算机学习应用程序采用统计学原理，使系统学习数据并使用以往的结果来确定将来的行为。 请参阅 [使用 Hadoop 上的 Mahout 生成电影推荐](hdinsight-mahout.md)。

### <a name="a-namemapreduceamapreduce"></a><a name="mapreduce"></a>MapReduce
MapReduce 是一个旧软件框架，用于编写并行批量处理大数据集的应用程序。 MapReduce 作业将分割大型数据集并将数据组织成键值对进行处理。

[YARN](#yarn) 是 Hadoop 下一代资源管理器和应用程序框架，也称为 MapReduce 2.0。 MapReduce 作业在 YARN 上运行。

有关 MapReduce 的详细信息，请参阅 Hadoop Wiki 中的 <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> 。

### <a name="a-nameoozieaoozie"></a><a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> 是一个管理 Hadoop 作业的工作流协调系统。 它与 Hadoop 堆栈集成，支持 MapReduce、Pig、Hive 和 Sqoop 的 Hadoop 作业。 它也能用于安排特定于某系统的作业，例如 Java 程序或 shell 脚本。 请参阅 [Use Oozie with Hadoop](hdinsight-use-oozie.md)（将 Oozie 与 Hadoop 配合使用）。

### <a name="a-namephoenixaphoenix"></a><a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> 是基于 HBase 的关系数据库层。 Phoenix 提供 JDBC 驱动程序以允许用户直接查询和管理 SQL 表。 Phoenix 将查询和其他语句转换为本机 NoSQL API 调用（而不是使用 MapReduce），因此可以在 NoSQL 存储之上实现更快的应用程序。 请参阅 [将 Apache Phoenix 和 SQuirreL 与 HBase 群集配合使用](hdinsight-hbase-phoenix-squirrel.md)。

### <a name="a-namepigapig"></a><a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> 是一个高级平台，允许你使用一种简单脚本语言（称为 Pig Latin）对超大型数据集执行复杂的 MapReduce 转换。 Pig 转换 Pig Latin 脚本，使其可以在 Hadoop 内运行。 可以创建用户定义的函数 (UDF) 来扩展 Pig Latin。 请参阅 [Use Pig with Hadoop](hdinsight-use-pig.md)（将 Pig 与 Hadoop 配合使用）。

### <a name="a-namesqoopasqoop"></a><a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> 是一种用于在 Hadoop 和关系数据库（如 SQL）或其他结构化数据存储之间尽可能高效地传输批量数据的工具。 请参阅 [将 Sqoop 与 Hadoop 配合使用](hdinsight-use-sqoop.md)。

### <a name="a-nametezatez"></a><a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> 是一个构建于 Hadoop YARN 之上的应用程序框架，用于执行复杂的非循环常规图形数据处理。 它是 MapReduce 框架的更灵活、功能更强大的后继，允许数据密集型进程（如 Hive）更高效地大规模运行。 请参阅 [“使用 Hive 和 HiveQL”中的“使用 Apache Tez 提高性能”](hdinsight-use-hive.md#usetez)。

### <a name="a-nameyarnayarn"></a><a name="yarn"></a>YARN
Apache YARN 是下一代 MapReduce（MapReduce 2.0 或 MRv2），它具有更大的可伸缩性和实时处理，支持超越 MapReduce 批处理的数据处理方案。 YARN 提供资源管理和分布式应用程序框架。 MapReduce 作业在 YARN 上运行。

若要了解 YARN，请参阅 <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop 下一代 MapReduce (YARN)</a>。

### <a name="a-namezookeeperazookeeper"></a><a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> 通过数据寄存器的共享层次结构命名空间 (znode) 协调大型分布式系统中的进程。 Znode 包含协调流程所需的少量元数据信息：状态、位置、配置，等等。

## <a name="programming-languages-on-hdinsight"></a>HDInsight 上的编程语言
HDInsight 群集（Hadoop、HBase、Storm 和 Spark 群集）支持多种编程语言，但某些编程语言默认情况下未安装。 对于默认情况下未安装的库、模块或程序包，请使用脚本操作来安装组件。 请参阅 [Script action development with HDInsight](hdinsight-hadoop-script-actions-linux.md)（使用 HDInsight 进行脚本操作开发）。

### <a name="default-programming-language-support"></a>默认编程语言支持
默认情况下，HDInsight 群集支持：

* Java
* Python

可以使用脚本操作安装其他语言： [Script action development with HDInsight](hdinsight-hadoop-script-actions-linux.md)（使用 HDInsight 进行脚本操作开发）。

### <a name="java-virtual-machine-jvm-languages"></a>Java 虚拟机 (JVM) 语言
除了 Java 外，许多语言可以使用 Java 虚拟机 (JVM) 运行；但是，运行其中某些语言可能需要在群集上安装其他组件。

HDInsight 群集支持以下基于 JVM 的语言：

* Clojure
* Jython (Python for Java)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop 特定的语言
HDInsight 群集支持以下特定于 Hadoop 生态系统的语言：

* 用于 Pig 作业的 Pig Latin
* 用于 Hive 作业的 HiveQL 和 SparkSQL

## <a name="a-nameadvantageaadvantages-of-hadoop-in-the-cloud"></a><a name="advantage"></a>Hadoop 在云中的优势
作为 Azure 云生态系统的一部分，HDInsight 中的 Hadoop 具有很多优势，其中包括：

* Hadoop 群集自动设置。 创建 HDInsight 群集比手动配置 Hadoop 群集容易得多。 有关详细信息，请参阅 [在 HDInsight 中预配 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。
* 最先进的 Hadoop 组件。 有关详细信息，请参阅 [HDInsight 中的 Hadoop 组件、版本和服务产品][component-versioning]。
* 群集具有高可用性和可靠性。 有关详细信息，请参阅 [HDInsight 中的 Hadoop 群集的可用性和可靠性](hdinsight-high-availability-linux.md) 。
* 使用 Azure Blob 存储（一种与 Hadoop 兼容的选项），数据存储高效又经济。 有关详细信息，请参阅 [将 Azure Blob 存储与 HDInsight 中的 Hadoop 配合使用](hdinsight-hadoop-use-blob-storage.md) 。
* 与其他 Azure 服务集成，包括 [Web 应用](https://azure.microsoft.com/documentation/services/app-service/web/)和 [SQL 数据库](https://azure.microsoft.com/documentation/services/sql-database/)。
* 用于运行 HDInsight 群集的其他 VM 大小和类型。 有关详细信息，请参阅 [HDInsight 中的 Hadoop 组件、版本和服务产品][component-versioning]。
* 群集缩放。 群集缩放使你能够更改正在运行的 HDInsight 群集的节点数，而无需删除或重新创建群集。
* 虚拟网络支持。 HDInsight 群集可用于 Azure 虚拟网络，以支持隔离云资源或将云资源与数据中心资源相链接的混合方案。
* 进入成本低。 开始[免费试用](https://azure.microsoft.com/free/)或查阅 [HDInsight 定价详细信息](https://azure.microsoft.com/pricing/details/hdinsight/)。

若要详细了解 HDInsight 中的 Hadoop 的优势，请参阅 [HDInsight 的 Azure 功能页][marketing-page]。

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight 标准版和 HDInsight 高级版
HDInsight 提供两个类别的大数据云产品：标准和高级。 HDInsight 标准版提供企业级群集，组织可使用它运行其大数据工作负荷。 HDInsight 高级版基于标准版生成，提供针对 HDInsight 群集的高级分析和安全功能。 有关详细信息，请参阅 [Azure HDInsight 高级版](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="a-idresourcesaresources-for-learning-more-about-big-data-analysis-hadoop-and-hdinsight"></a><a id="resources"></a>用于详细了解大数据分析、Hadoop 和 HDInsight 的资源
使用以下资源在此介绍云中 Hadoop 和大数据分析的简介的基础上构建。

### <a name="hadoop-documentation-for-hdinsight"></a>HDInsight 的 Hadoop 文档
* [HDInsight 文档](https://azure.microsoft.com/documentation/services/hdinsight/)：Azure HDInsight 上的 Hadoop 文档页，包含文章、视频及其他资源的链接。
* [Get started with Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)（HDInsight 中的 Hadoop 入门）：有关预配 HDInsight Hadoop 群集和运行示例 Hive 查询的快速入门教程。
* [HDInsight 中的 Spark 入门](hdinsight-apache-spark-jupyter-spark-sql.md)：有关创建 Spark 群集和运行交互式 Spark SQL 查询的快速入门教程。
* [在 HDInsight 上使用 R Server](hdinsight-hadoop-r-server-get-started.md)：开始在 HDInsight 高级版中使用 R Server。
* [Provision HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md)（预配 HDInsight 群集）：了解如何通过 Azure 门户、Azure CLI 或 Azure PowerShell 预配 HDInsight Hadoop 群集。

### <a name="apache-hadoop"></a>Apache Hadoop
* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>：了解有关 Apache Hadoop 软件库的详细信息，这是一个框架，允许你跨计算机群集分布式处理大型数据集。
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>：详细了解 Hadoop 分布式文件系统的体系结构和设计，Hadoop 分布式文件系统是由 Hadoop 应用程序使用的主存储系统。
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce 教程</a>：了解有关编程框架的详情，此编程框架用于编写 Hadoop 应用程序，以便并行地快速处理大型计算节点群集上的大量数据。

### <a name="microsoft-business-intelligence"></a>Microsoft 商业智能
大家熟悉的商业智能 (BI) 工具（如 Excel、PowerPivot、SQL Server Analysis Services 和 SQL Server Reporting Services）使用 Power Query 外接程序或 Microsoft Hive ODBC 驱动程序来检索、分析和报告与 HDInsight 集成的数据。

这些 BI 工具可有助于大数据分析：

* [使用 Power Query 将 Excel 连接到 Hadoop](hdinsight-connect-excel-power-query.md)：了解如何使用 Microsoft Power Query for Excel，将 Excel 连接到存储 HDInsight 群集关联数据的 Azure 存储帐户。 所需的 Windows 工作站。 使用基于 Windows 或 Linux 的群集。
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)：了解如何使用 Microsoft Hive ODBC 驱动程序从 HDInsight 导入数据。 所需的 Windows 工作站。 使用基于 Windows 或 Linux 的群集。
* [Microsoft 云平台](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx)：了解有关 Power BI for Office 365、下载 SQL Server 试用版，以及设置 SharePoint Server 2013 和 SQL Server BI 的信息。
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)。
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)。

[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/



<!--HONumber=Dec16_HO2-->


