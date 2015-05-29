<properties 
   pageTitle="Hadoop 简介：大数据分析 | Microsoft Azure" 
   description="HDInsight 在云中的 Apache Hadoop 组件简介。了解 HDInsight 如何使用 Hadoop 群集来管理、分析和报告大数据。" 
   services="hdinsight" 
   documentationCenter="" 
   authors="cjgronlund" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.tgt_pltfrm="na" 
   ms.workload="big-data" 
   ms.date="02/18/2015" 
   wacn.date="" 
   ms.author="cgronlun"/>


# HDInsight 中的 Hadoop 简介：云中的大数据处理和分析

了解 Azure HDInsight 中的 Hadoop 生态系统 - 组件、常见术语和解决方案。此外，了解有关使用 HDInsight 中的 Hadoop 的文档、教程和资源。

## HDInsight 中的 Hadoop 是什么？

Azure HDInsight 以云方式部署并设置 Apache Hadoop 群集，从而提供旨在对大数据进行管理、分析和报告的软件框架。Hadoop 内核提供了带 Hadoop 分布式文件系统 (HDFS) 的可靠数据存储和一个简单的 MapReduce 编程模型，该模型用于并行处理和分析存储在此分布式系统中的数据。 


## 什么是大数据？
大数据指的是具有以下特性的数据：收集的数量不断增长、收集速度日益加快、采用的非结构化格式的类型不断扩展且语义上下文灵活多变。 

大数据描述各种大型数字信息，从 Twitter 源中的文本到工业设备的传感器信息再到客户浏览在线产品目录并购买的信息。大数据可以是历史数据（即已存储的数据），也可以是实时数据（即从数据源直接流式传输的数据）。 

若要使大数据成为可付诸行动的情报或见解，不仅必须提出正确的问题，还必须收集与这些问题相关的数据，这些数据必须可供访问、清理和分析，然后以有用的方式呈现。这正是 HDInsight 中的 Hadoop 用武之地。


## <a name="overview"></a>HDInsight 中的 Hadoop 生态系统概述

Apache Hadoop 是一种迅速扩展的技术堆栈，是大数据分析的首选解决方案。HDInsight 是 Microsoft Azure 在云中实现 Hadoop 的框架。它包括 Storm、HBase、Pig、Hive、Sqoop、Oozie、Ambari 等的实现。HDInsight 还可集成商业智能 (BI) 工具，例如 Excel、SQL Server Analysis Services 和 SQL Server Reporting Services。


* Azure HDInsight 使用 **Linux** 或 **Windows** 作为基础操作系统，在云中部署并设置 Hadoop 群集。

	* **Linux 上的 HDInsight（预览版）** - Ubuntu 上的 Hadoop 群集。如果你熟悉 Linux 或 Unix、要从现有的基于 Linux 的 Hadoop 解决方案进行迁移，或者想要轻松集成针对 Linux 所构建的 Hadoop 生态系统组件，请使用此群集。

	* **Windows 上的 HDInsight** - Windows Server 上的 Hadoop 群集。如果你熟悉 Windows、要从现有的基于 Windows 的 Hadoop 解决方案迁移，或者想要与 .NET 或其他 Windows 功能集成，请使用此选项。
 
	下表列出了两者之间的比较：

	类别 | Linux 上的 HDInsight | Windows 上的 HDInsight 
	---------| -------------------| --------------------
	**群集操作系统** | Ubuntu 12.04 长期支持 (LTS) | Windows Server 2012 R2
	**群集类型** | Hadoop | Hadoop、HBase、Storm
	**部署** | Azure 管理门户、跨平台命令行、Azure PowerShell | Azure 管理门户、跨平台命令行、Azure PowerShell、HDInsight .NET SDK
	**群集 UI** | Ambari | 群集仪表板
	**远程访问** | 安全外壳 (SSH) | 远程桌面协议 (RDP)


* HDInsight 使用 **Hortonworks 数据平台 (HDP)** Hadoop 分发版。
 
* Apache Hadoop 是一个用于大数据管理和分析的软件框架。HDInsight 针对特定工作负载提供多种配置，你也可以<a href="/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">使用脚本操作自定义群集</a>。

	* **Hadoop for HDInsight** - Apache Hadoop 内核提供了带 [HDFS] 的可靠数据存储(#HDFS)和一个简单的 [MapReduce](#mapreduce)  编程模型，该模型用于并行处理和分析数据。
	
	* **HBase for HDInsight** - [HBase](#hbase) 是构建于 Hadoop 上的 Apache 开源 NoSQL 数据库，用于为大量非结构化和半结构化数据提供随机访问和高度一致性。
	
	* **Storm for HDInsight** - [Storm](#storm) 是分布式可容错开源计算系统，可用来实时处理数据。

除了以前的整体配置，HDInsight 群集还包括下列各个组件。

* **[Ambari](#ambari)**  - 群集设置、管理和监视。

* **[Avro](#avro)** (Microsoft .NET Library for Avro) - Microsoft .NET 环境的数据序列化。

* **[Hive](#hive)** - 类似于结构化查询语言 (SQL) 的查询。

* **[Mahout](#mahout)** - 机器学习。

* **[MapReduce 和 YARN](#mapreduce)** - 分布式处理和资源管理。

* **[Oozie](#oozie)** - 工作流管理。

* **[Pig](#pig)** - 更简单的 MapReduce 转换脚本。

* **[Sqoop](#sqoop)** - 数据导入和导出。

* **[ZooKeeper](#zookeeper)** - 协调分布式系统中的流程。

> [AZURE.NOTE] 有关特定组件和版本的信息，请参阅 [HDInsight 提供的 Hadoop 群集版本有哪些新功能？][component-versioning]

### <a name="ambari"></a>Ambari

Apache Ambari 用于设置、管理和监视 Apache Hadoop 群集。它包括一系列直观的操作员工具和一组隐藏 Hadoop 复杂性的可靠 API，可简化群集操作。请参阅[使用 Ambari 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari)（仅限 Linux）、[使用 Ambari API 监视 HDInsight 中的 Hadoop 群集](hdinsight-monitor-use-ambari-api)和 <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API 参考</a>。

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)

Microsoft .NET Library for Avro 针对 Microsoft.NET 环境序列化实现了 Apache Avro 紧凑的二进制数据交换格式。它使用 <a target="_blank" href="http://www.json.org/">JavaScript 对象表示法 (JSON)</a> 定义与语言无关的架构，以支持语言互操作性，这意味着以一种语言序列化的数据可以用另一种语言读取。有关格式的详细信息可以在 <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro 规范</a>中找到。 
Avro 文件格式支持分布式 MapReduce 编程模型。文件是"可拆分的"，也就是说，你可以在文件中任意设置一个点，然后即可从某一特定块开始读取。若要了解相关方法，请参阅[使用 Microsoft .NET Library for Avro 将数据序列化](hdinsight-dotnet-avro-serialization)。

### <a name="hbase"></a>HBase

<a target="_blank" href="http://hbase.apache.org/">Apache HBase</a> 是构建于 Hadoop 上的一个非关系数据库，专为海量非结构化和半结构化数据而设计，这类数据可能是几十亿行乘几百万列。HDInsight 上的 HBase 群集已配置为将数据直接存储在 Azure Blob 存储中，既减少了延迟又提高了灵活性。请参阅 [HDInsight 上的 HBase 概述](hdinsight-hbase-overview)。

### <a name="hdfs"></a>HDFS

Hadoop 分布式文件系统 (HDFS) 是一种分布式文件系统，采用 MapReduce 和 YARN，是 Hadoop 生态系统的核心。HDFS 是 HDInsight 上 Hadoop 群集的标准文件系统。

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> 是构建于 Hadoop 上的一个数据仓库软件，允许使用类似于 SQL 的语言 HiveQL 来查询和管理分布式存储系统中的大型数据集。Hive（类似于 Pig）是一种基于 MapReduce 的抽象。在运行时，Hive 会将查询转换为一系列 MapReduce 作业。Hive 比 Pig 在概念上更接近于关系数据库管理系统，因此适用于结构化程度更高的数据。对于非结构化数据，Pig 是更佳的选择。请参阅[将 Hive 与 HDInsight 中的 Hadoop 配合使用](hdinsight-use-hive)。

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> 是在 Hadoop 上运行的一种可扩展的计算机学习算法库。计算机学习应用程序采用统计学原理，使系统学习数据并使用以往的结果来确定将来的行为。请参阅[使用 Hadoop 上的 Mahout 生成电影推荐](hdinsight-mahout)。

### <a name="mapreduce"></a>MapReduce 和 YARN
Hadoop MapReduce 是一个用于编写并行处理大数据集的应用程序的软件框架。MapReduce 作业将分割大型数据集并将数据组织成键值对进行处理。 

Apache YARN 是下一代 MapReduce（MapReduce 2.0 或 MRv2），用于将 JobTracker 的两个主要任务（资源管理和作业计划/监视）分割成单独的实体。

有关 MapReduce 的更多信息，请参阅 Hadoop Wiki 中的 <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a>。若要了解 YARN，请参阅 <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop 下一代 MapReduce (YARN)</a>。

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> 是一个管理 Hadoop 作业的工作流/协调系统。它与 Hadoop 堆栈集成，支持 MapReduce、Pig、Hive 和 Sqoop 的 Hadoop 作业。它也能用于安排特定于某系统的作业，例如 Java 程序或 shell 脚本。请参阅[将基于时间的 Oozie 协调器与 HDInsight 中的 Hadoop 配合使用](hdinsight-use-oozie-coordinator-time)。

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> 是一个高级平台，可以使用一种简单脚本语言 Pig Latin 对超大型数据集执行复杂的 MapReduce 转换。Pig 转换 Pig Latin 脚本，使其可以在 Hadoop 内运行。可以创建用户定义的函数 (UDF) 来扩充 Pig Latin。请参阅[将 Pig 与 Hadoop 配合使用来分析 Apache 日志文件](hdinsight-use-pig)。

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> 是一种用于在 Hadoop 和关系数据库（如 SQL）或其他结构化数据存储之间尽可能高效地传输批量数据的工具。请参阅[将 Sqoop 与 Hadoop 配合使用](hdinsight-use-sqoop)。

### <a name="storm"></a>Storm
<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a> 是一个分布式实时计算系统，用于快速处理大型数据流。Storm 以 HDInsight 中的托管群集形式提供。请参阅[使用 Storm 和 Hadoop 分析实时传感器数据](hdinsight-storm-sensor-data-analysis)。

### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> 通过数据寄存器的共享层次结构命名空间 (znode) 协调大型分布式系统中的流程。Znode 包含协调流程所需的少量元数据信息：状态、位置、配置，等等。 

## <a name="advantage"></a>Hadoop 在云中的优势

作为 Azure 云生态系统的一部分，HDInsight 中的 Hadoop 具有很多优势，其中包括：

* Hadoop 群集自动设置。创建 HDInsight 群集比手动配置 Hadoop 群集容易得多。有关详细信息，请参阅[在 HDInsight 中设置 Hadoop 群集](hdinsight-provision-clusters)。

* 最先进的 Hadoop 组件。有关详细信息，请参阅 [HDInsight 提供的 Hadoop 群集版本有哪些新功能？][component-versioning]。

* 群集具有高可用性和可靠性。有关详细信息，请参阅[ HDInsight 中的 Hadoop 群集的可用性和可靠性](hdinsight-high-availability)。

* 使用 Azure Blob 存储（一种与 Hadoop 兼容的选项），数据存储高效又经济。有关详细信息，请参阅[将 Azure Blob 存储与 HDInsight 中的 Hadoop 配合使用](hdinsight-use-blob-storage)。

* 与其他 Azure 服务集成，包括[网站](/documentation/services/websites/)和 [SQL Database](/documentation/services/sql-database/)。

* 进入成本低。开始[免费试用](/pricing/1rmb-trial/)或查阅 [HDInsight 定价详细信息](/home/features/hdinsight/#price)。

若要详细了解 HDInsight 中的 Hadoop 的优势，请参阅 [HDInsight 的 Azure 功能页面][marketing-page]。



## <a id="resources"></a>用于详细了解大数据分析、Hadoop 和 HDInsight 的资源


### Linux 上的 HDInsight（预览版）

* [Linux 上的 HDInsight 入门](hdinsight-hadoop-linux-get-started) - 在 Linux 上设置 HDInsight Hadoop 群集和运行示例 Hive 查询的快速入门教程。

* [使用自定义选项在 Linux 上设置 HDInsight](hdinsight-hadoop-provision-linux-clusters) - 了解如何通过 Azure 管理门户、Azure 跨平台命令行或 Azure PowerShell，使用自定义选项在 Linux 上设置 HDInsight Hadoop 群集。

* [使用 Linux 上的 HDInsight](hdinsight-hadoop-linux-information) - 获取与 Hadoop Linux 一起使用的一些快速技巧在 Azure 上设置的群集。

* [使用 Ambari 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari) - 了解如何使用 Ambari Web 或 Ambari REST API 在 HDInsight 群集上监视和管理基于 Linux 的 Hadoop。


### Windows 上的 HDInsight

* [HDInsight 文档](/documentation/services/hdinsight/) - Azure HDInsight 的文档页，带有文章、视频及其他资源的链接。
	
* [HDInsight 学习路线图](hdinsight-learn-map) - HDInsight 的 Hadoop 文档导航。

* [Azure HDInsight 入门](hdinsight-get-started) - 关于使用 HDInsight 中的 Hadoop 的快速入门教程。

* [运行 HDInsight 示例](hdinsight-run-samples) - 有关如何运行随 HDInsight 提供的示例的教程。
	
* [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx) - HDinsight SDK 的参考文档。


### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a> - 了解有关 Apache Hadoop 软件库的详细信息，这是一个框架，允许你跨计算机群集分布式处理大型数据集。	

* <a target="_blank" href="http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html">HDFS</a> - 了解有关 Hadoop 分布式文件系统的体系结构和设计，Hadoop 分布式文件系统是由 Hadoop 应用程序使用的主存储系统。		

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce 教程</a> - 了解有关编程框架的详情，此编程框架用于编写 Hadoop 应用程序，以便以并行方式快速处理大型计算节点群集上的大量数据。	

### Azure 上的 SQL Database	
		
* [Azure SQL Database](https://msdn.microsoft.com/zh-cn/library/azure/ee336279.aspx) - 有关 SQL Database 的 MSDN 文档。
	
* [SQL Database 的管理门户](https://msdn.microsoft.com/zh-cn/library/azure/dn771027.aspx) - 一种轻量版易用型数据库管理工具，用于以云方式管理 SQL Database。
	
* [Adventure Works for SQL Database](http://msftdbprodsamples.codeplex.com/releases/view/37304) - SQL Database 示例数据库的下载页。	

### Microsoft 商业智能（适用于 Windows 上的 HDInsight）

大家熟悉的商业智能 (BI) 工具（如 Excel、PowerPivot、SQL Server Analysis Services 和 SQL Server Reporting Services）使用 Power Query 外接程序或 Microsoft Hive ODBC 驱动程序来检索、分析和报告与 HDInsight 集成的数据。 

这些 BI 工具可有助于大数据分析：

* [利用 Power Query 将 Excel 连接到 Hadoop](hdinsight-connect-excel-power-query) - 了解如何使用 Microsoft Power Query for Excel，将 Excel 连接到存储 HDInsight 群集关联数据的 Azure 存储帐户。 

* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Hadoop](hdinsight-connect-excel-hive-ODBC-driver) - 了解如何使用 Microsoft Hive ODBC 驱动程序从 HDInsight 导入数据。
			
* [Microsoft 云平台](http://www.microsoft.com/zh-cn/server-cloud/solutions/business-intelligence/default.aspx) - 了解适用于 Office 365 的 Power BI，下载 SQL Server 试用版，以及设置 SharePoint Server 2013 和 SQL Server BI。

* <a target="_blank" https://msdn.microsoft.com/zh-cn/library/hh231701.aspx">详细了解 SQL Server Analysis Services</a>。

* <a target="_blank" href="http://msdn.microsoft.com/zh-cn/library/ms159106.aspx">了解 SQL Server Reporting Services</a>。


### 尝试使用 HDInsight 解决方案来分析大数据（适用于 Widows 上的 HDInsight）

分析组织的数据，获得业务的深度见解。下面是一些示例： 

* [分析 HVAC 传感器数据](hdinsight-hive-analyze-sensor-data) - 了解如何通过将 Hive 与 HDInsight (Hadoop) 配合使用来分析传感器数据，然后在 Microsoft Excel 中实现数据的可视化。在此例中，你将使用 Hive 处理 HVAC 系统生成的历史数据，从而了解哪些系统无法可靠地维持设定的温度。

* [将 Hive 与 HDInsight 配合使用来分析网站日志](hdinsight-hive-analyze-website-log) - 了解如何使用 HDInsight 中的 HiveQL 来分析网站日志，从而了解来自外部网站的一天访问次数以及用户遇到的网站错误汇总。

* [使用 HDInsight (Hadoop) 中的 Storm 和 HBase 分析实时传感器数据](hdinsight-storm-sensor-data-analysis.md) - 了解如何构建一个解决方案，使用 HDInsight 中的 Storm 群集处理 Azure 事件中心的传感器数据，然后将处理后的传感器数据以近乎实时的信息显示在基于 Web 的仪表板上。

若要试用 HDInsight 上的 Hadoop，请参阅 [HDInsight 文档页](/documentation/services/hdinsight/)上"探究"部分的"入门"文章。若要尝试更高级的示例，请向下滚动到"分析"部分。


[marketing-page]: /home/features/hdinsight/
[component-versioning]: /documentation/articles/hdinsight-component-versioning
[zookeeper]: http://zookeeper.apache.org/ 

<!---HONumber=56-->