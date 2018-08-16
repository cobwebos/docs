---
title: 在 HDInsight 中使用 Hadoop Pig
description: 了解如何将 Pig 与 Hadoop on HDInsight 配合使用。
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: 7f469efb536f8c2dfc95cfe1770544b93c06b29f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597247"
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>将 Pig 与 Hadoop on HDInsight 配合使用

了解如何将 [Apache Pig](http://pig.apache.org/) 与 HDInsight 配合使用。

Pig 是一个平台，可以使用名为 *Pig Latin* 的过程语言为 Hadoop 创建程序。 Pig 可以替代 Java 来创建 *MapReduce* 解决方案，它已包括在 Azure HDInsight 中。 使用下表可以找出将 Pig 与 HDInsight 配合使用的各种方法：

| **使用此方法**，如果想要... | ...**交互式** shell | ...**批处理** | ...使用此**群集操作系统** | ...从此**客户端操作系统** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux、Unix、Mac OS X 或 Windows |
| [REST API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux 或 Windows |Linux、Unix、Mac OS X 或 Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux 或 Windows |Windows（暂时） |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux 或 Windows |Windows |

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a id="why"></a>使用 Pig 的原因

在 Hadoop 中使用 MapReduce 来处理数据时，其中一项难题是仅使用映射和化简函数来实现处理逻辑。 进行复杂的处理时，通常必须将处理分解成多个 MapReduce 操作，这些操作合在一起即可获得所需的结果。

使用 Pig，可以将处理定义成一系列转换，相关数据经过这些转换即可生成所需的输出。

使用 Pig Latin 语言，可以通过一个或多个转换从原始输入描述数据流，以便生成所需的输出。 Pig Latin 程序遵循下述常规模式：

* **加载**：从文件系统中读取要操作的数据

* **转换**：操作数据

* **转储或存储**：将数据输出到屏幕或将其存储后再进行处理

### <a name="user-defined-functions"></a>用户定义的函数

Pig Latin 还支持使用用户定义函数 (UDF) 来调用外部组件，以便实现难以在 Pig Latin 中建模的逻辑。

有关 Pig Latin 的详细信息，请参阅 [Pig Latin Reference Manual 1](http://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html)（Pig Latin 参考手册 1）和 [Pig Latin Reference Manual 2](http://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html)（Pig Latin 参考手册 2）。

如需通过 Pig 使用 UDF 的示例，请参阅以下文档：

* [在 HDInsight 中通过 Pig 使用 DataFu](apache-hadoop-use-pig-datafu-udf.md) - DataFu 是由 Apache 维护的有用 UDF 的集合
* [在 HDInsight 中将 Python 与 Pig 和 Hive 配合使用](python-udf-hdinsight.md)
* [在 HDInsight 中将 C# 与 Hive 和 Pig 配合使用](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>示例数据

HDInsight 提供各种示例数据集，它们存储在 `/example/data` 和 `/HdiSamples` 目录中。 这些目录位于群集的默认存储中。 本文档中的 Pig 示例使用来自 `/example/data/sample.log` 的 *log4j* 文件。

该文件中的每个日志都包含一行字段，其中包含一个 `[LOG LEVEL]` 字段，用于显示类型和严重性，例如：

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

在前面的示例中，日志级别为 ERROR。

> [!NOTE]
> 还可以使用 [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) 日志记录工具来生成 log4j 文件，然后将该文件上传到 Blob。 请参阅[将数据上传到 HDInsight](../hdinsight-upload-data.md) 以获取相关说明。 有关如何将 Azure 存储中的 Blob 与 HDInsight 配合使用的详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用](../hdinsight-hadoop-use-blob-storage.md)。

## <a id="job"></a>示例作业

下面的 Pig Latin 作业从 HDInsight 群集的默认存储加载 `sample.log` 文件。 然后，它会执行一系列转换，对输入数据中出现的每个日志级别进行计数。 结果会写入 STDOUT。

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

下图概要显示每个转换对数据的影响。

![转换的图形表示形式][image-hdi-pig-data-transformation]

## <a id="run"></a>运行 Pig Latin 作业

HDInsight 可以使用各种方法来运行 Pig Latin 作业。 使用下表来确定哪种方法最适合，并按链接进行演练。

| **使用此方法**，如果想要... | ...**交互式** shell | ...**批处理** | ...使用此**群集操作系统** | ...通过此**客户端** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux、Unix、Mac OS X 或 Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux 或 Windows |Linux、Unix、Mac OS X 或 Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux 或 Windows |Windows（暂时） |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux 或 Windows |Windows |

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a name="pig-and-sql-server-integration-services"></a>Pig 和 SQL Server Integration Services

可以使用 SQL Server Integration Services (SSIS) 来运行 Pig 作业。 Azure Feature Pack for SSIS 提供适用于 HDInsight 上的 Pig 作业的以下组件。

* [Azure HDInsight Pig 任务][pigtask]

* [Azure 订阅连接管理器][connectionmanager]

在[此处][ssispack]了解有关 Azure Feature Pack for SSIS 的详细信息。

## <a id="nextsteps"></a>后续步骤
现在，已了解如何将 Pig 与 HDInsight 配合使用，请使用以下链接来学习 Azure HDInsight 的其他用法。

* [将数据上传到 HDInsight](../hdinsight-upload-data.md)
* [将 Hive 与 HDInsight 配合使用][hdinsight-use-hive]
* [将 Sqoop 与 HDInsight 配合使用](hdinsight-use-sqoop.md)
* [将 Oozie 与 HDInsight 配合使用](../hdinsight-use-oozie.md)
* [将 MapReduce 作业与 HDInsight 配合使用][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
