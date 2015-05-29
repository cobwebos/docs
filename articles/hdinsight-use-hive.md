<properties
   pageTitle="在 HDInsight 中使用 Hadoop Hive | Azure"
   description="了解如何将 Hive 与 HDInsight 上的 Hadoop 配合使用。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/23/2015"
   wacn.date="" 
   ms.author="larryfr"/>

# 将 Hive 与 HDInsight 上的 Hadoop 配合使用

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

[Apache Hive](http://hive.apache.org/) 是适用于 Hadoop 的数据仓库系统，可让你使用 HiveQL（类似于 SQL 的查询语言）来进行数据汇总、查询和数据分析。使用 Hive 能够以交互方式浏览数据，或者创建可重用的批处理作业。

在本文中，你将学习如何将 Hive 与 hdinsight 配合使用。

## <a id="why"></a>为何要使用 Hive？

Hive 允许你在很大程度上未结构化的数据上投影结构。在定义结构后，你可以使用 Hive 来查询这些数据，而不需要具备 Java 或 MapReduce 的知识。**HiveQL**（Hive 查询语言）可让你使用类似于 T-SQL 的语句编写查询。

Hive 知道如何处理结构化和半结构化文档，例如其中的字段以特定字符分隔的文本文件。Hive 还支持对复杂或不规则的结构化数据使用自定义**序列化程序/反序列化程序 (SerDe)**。有关详细信息，请参阅[如何将自定义 JSON SerDe 与 hdinsight 配合使用](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx)。

还可以通过**用户定义函数的 (UDF)** 扩展 Hive。UDF 允许你实现 HiveQL 中不容易建模的功能或逻辑。有关将 UDF 与 Hive 配合使用的示例，请参阅：

* [在 HDInsight 中将 Python 与 Hive 和 Pig 配合使用](hdinsight-python)

* [在 HDInsight 中将 C# 与 Hive 和 Pig 配合使用](hdinsight-hadoop-hive-pig-udf-dotnet-csharp)

* [如何将自定义 Hive UDF 添加到 HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a id="data"></a>关于示例数据

本示例使用了一个 *log4j*示例文件，该文件存储在 Blob 存储容器中的 **/example/data/sample.log** 内。该文件中的每个日志都包含一行字段，其中包含一个 `[LOG LEVEL]` 字段，可显示类型和严重性，例如：

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

在上面的示例中，日志级别为 ERROR。

> [AZURE.NOTE] 你也可以使用 [Apache Log4j](http://zh.wikipedia.org/wiki/Log4j) 日志记录工具生成 log4j 文件，然后将该文件上载到 Blob 容器。有关说明，请参阅[将数据上载到 HDInsight](hdinsight-upload-data)。有关如何将 Azure Blob 存储与 HDInsight 配合使用的详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用](hdinsight-use-blob-storage)。

示例数据存储在 HDInsight 用作默认文件系统的 Azure Blob 存储中。HDInsight 可以使用 **wasb** 前缀访问 Blob 中存储的文件。例如，若要访问 sample.log 文件，可以使用以下语法：

	wasb:///example/data/sample.log

由于 WASB 是 HDInsight 的默认存储，因此你也可以使用 HiveQL 中的 **/example/data/sample.log** 访问文件。

> [AZURE.NOTE] 上述语法 **wasb:///** 可用来访问 HDInsight 群集的默认存储容器所存储的文件。如果你在设置群集时指定了其他存储帐户，并想要访问这些帐户中存储的文件，你可以指定容器名称和存储帐户地址来访问数据，例如，**wasb://mycontainer@mystorage.blob.core.chinacloudapi.cn/example/data/sample.log**。

## <a id="job"></a>关于示例作业

以下 HiveQL 语句将列投影到 **wasb:///example/data** 目录中存储的分隔数据：

	DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

在上例中，HiveQL 语句执行以下操作：

* **DROP TABLE**：删除表和数据文件（如果表已存在）。
* **CREATE EXTERNAL TABLE**：在 Hive 中创建一个新的**外部**表。外部表只会在 Hive 中存储表定义；数据以原始格式保留在原始位置。
* **ROW FORMAT**：告知 Hive 如何设置数据的格式。在此情况下，每个日志中的字段以空格分隔。
* **STORED AS TEXTFILE LOCATION**：让 Hive 知道数据的存储位置（example/data 目录），并且数据已存储为文本。数据可以在一个文件中，也可以分散在目录的多个文件内。
* **SELECT**：选择其列 **t4** 包含值 **[ERROR]** 的所有行计数。这应会返回值 **3**，因为有三个行包含此值。

> [AZURE.NOTE] 当你预期以外部源更新基础数据（例如自动化数据上载过程），或以其他 MapReduce 操作更新基础数据，但希望 Hive 查询始终使用最新数据时，必须使用外部表。
	>
> 删除外部表**不会**删除数据，只会删除表定义。

创建外部表后，使用以下语句创建**内部**表。

	CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
	STORED AS ORC;
	INSERT OVERWRITE TABLE errorLogs
	SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

这些语句将执行以下操作：

* **CREATE TABLE IF NOT EXISTS**：如果表不存在，则创建表。由于未使用 **EXTERNAL** 关键字，因此这是一个内部表，它存储在 Hive 数据仓库中并完全受 Hive 的管理。
* **STORED AS ORC**：以优化行纵栏表 (ORC) 格式存储数据。这是高度优化且有效的 Hive 数据存储格式。
* **INSERT OVERWRITE ...SELECT**：从包含 **[ERROR]** 的 **log4jLogs** 表中选择行，然后将数据插入 **errorLogs** 表。

> [AZURE.NOTE] 与外部表不同，删除内部表会同时删除基础数据。

## <a id="usetez"></a>使用 Tez 提高性能

[Apache Tez](http://tez.apache.org) 是可让数据密集型应用程序（例如 Hive）大规模高效运行的框架。在最新版的 HDInsight 中，Hive 支持在 Tez 上运行。此功能目前默认为关闭，而必须启用。若要充分利用 Tez，你必须设置 Hive 查询的以下值：

	set hive.execution.engine=tez;

你可为每个查询提交此值，只需将它放置在查询的开头即可。你也可以在创建群集时设置配置值，而在群集上将此值默认为打开。可以在[设置 HDInsight 群集](hdinsight-provision-clusters)中找到详细信息。

[Tez 上的 Hive 设计文档](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)包含实现选项和优化配置的详细信息。


## <a id="run"></a>运行 HiveQL 作业

HDInsight 可以使用各种方法运行 HiveQL 作业。使用下表来确定哪种方法最适合你，然后单击相关演练的链接。

| **使用此方法** 如果你想要...                                                     | ...**交互式** shell | ...**批**处理 | ...使用此**群集操作系统** | ...从此**客户端操作系统** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-hive-ssh)                                         |              ✔              |            ✔            | Linux                                     | Linux、Unix、Mac OS X 或 Windows        |
| [Curl](hdinsight-hadoop-use-hive-curl)                                       |           &nbsp;            |            ✔            | Linux 或 Windows                          | Linux、Unix、Mac OS X 或 Windows        |
| [查询控制台](hdinsight-hadoop-use-hive-query-console)                     |           &nbsp;            |            ✔            | Windows                                   | 基于浏览器                            |
| [HDInsight Tools for Visual Studio](hdinsight-hadoop-use-hive-visual-studio) |           &nbsp;            |            ✔            | Linux 或 Windows                          | Windows                                  |
| [.NET SDK for Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk)                   |           &nbsp;            |            ✔            | Linux 或 Windows                          | Windows（暂时）                        |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell)                   |           &nbsp;            |            ✔            | Linux 或 Windows                          | Windows                                  |
| [远程桌面](hdinsight-hadoop-use-hive-remote-desktop)                   |              ✔              |            ✔            | Windows                                   | Windows                                  |

## <a id="nextsteps"></a>后续步骤

现在，你已了解如何将 Hive 与 HDInsight 配合使用，请使用以下链接来学习 Azure HDInsight 的其他用法。

* [将数据上载到 HDInsight][hdinsight-upload-data]
* [将 Pig 与 HDInsight 配合使用][hdinsight-use-pig]
* [将 MapReduce 作业与 HDInsight 配合使用][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[1]: hdinsight-hadoop-visual-studio-tools-get-started

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/zh-cn/library/dn479185.aspx



[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: /documentation/articles/hdinsight-connect-excel-power-query/

[hdinsight-use-pig]: hdinsight-use-pig
[hdinsight-use-oozie]: hdinsight-use-oozie
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce


[hdinsight-storage]: hdinsight-use-blob-storage

[hdinsight-provision]: hdinsight-provision-clusters
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically
[hdinsight-upload-data]: hdinsight-upload-data
[hdinsight-get-started]: hdinsight-get-started

[Powershell-install-configure]: install-configure-powershell
[powershell-here-strings]: http://technet.microsoft.com/zh-cn/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=56-->