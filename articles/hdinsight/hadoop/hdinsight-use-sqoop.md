---
title: 通过 Azure HDInsight (Apache Hadoop) 运行 Apache Sqoop 作业
description: 了解如何使用工作站中的 Azure PowerShell 在 Hadoop 群集与 Azure SQL 数据库之间运行 Sqoop 导入和导出。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565846"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>在 HDInsight 中将 Apache Sqoop 与 Hadoop 配合使用
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何在 HDInsight 中使用 Apache Sqoop 导入和导出的 HDInsight 群集和 Azure SQL 数据库之间的数据。

尽管 Apache Hadoop 是一个适合用于处理非结构化和半结构化数据，例如日志和文件，还可能需要处理关系数据库中存储的结构化的数据。

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html)是专用于在 Hadoop 群集和关系数据库之间传输数据的工具。 可以使用此工具将数据从关系数据库管理系统 (RDBMS)（如 SQL Server、MySQL 或 Oracle）中导入到 Hadoop 分布式文件系统 (HDFS)，在 Hadoop 中使用 MapReduce 或 Apache Hive 转换数据，然后将数据导回 RDBMS。 在本文中，用于关系数据库使用 SQL Server 数据库。

> [!IMPORTANT]  
> 这篇文章设置测试环境，以执行数据传输。 然后从节中的方法之一选择此环境的数据传输方法[运行 Sqoop 作业](#run-sqoop-jobs)，则可以进一步下面。

有关 HDInsight 群集支持的 Sqoop 版本，请参阅[什么是 HDInsight 提供的群集版本中的新增功能？](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>了解方案

HDInsight 群集带有某些示例数据。 可使用以下两个示例：

* 一个 Apache Log4j 日志文件，其中位于`/example/data/sample.log`。 以下日志会从该文件中提取出来：

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* 名为的 Hive 表`hivesampletable`，它引用位于的数据文件`/hive/warehouse/hivesampletable`。 该表包含一些移动设备数据。
  
  | 字段 | 数据类型 |
  | --- | --- |
  | clientid |字符串 |
  | querytime |字符串 |
  | market |字符串 |
  | deviceplatform |字符串 |
  | devicemake |字符串 |
  | devicemodel |字符串 |
  | state |字符串 |
  | country |字符串 |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

在本文中，使用这两个数据集测试 Sqoop 导入和导出。

## <a name="create-cluster-and-sql-database"></a>设置测试环境
通过使用 Azure 资源管理器模板在 Azure 门户创建群集、 SQL 数据库和其他对象。 可以在中找到的模板[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)。 资源管理器模板调用 bacpac 包以将表架构部署到 SQL 数据库。  bacpac 包位于公共 blob 容器 https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac 中。 如果想要为 bacpac 文件使用私有容器，请使用模板中的以下值：

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> 使用模板或 Azure 门户进行的导入操作仅支持从 Azure Blob 存储导入 BACPAC 文件。

1. 选择要在 Azure 门户中打开资源管理器模板的以下映像。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 输入以下属性：

    |字段 |值 |
    |---|---|
    |订阅 |从下拉列表中选择你的 Azure 订阅。|
    |资源组 |从下拉列表中，选择你的资源组或创建一个新|
    |位置 |从下拉列表中选择一个区域。|
    |群集名称 |输入 Hadoop 群集的名称。 使用仅小写字母。|
    |群集登录用户名 |保留预填充的值`admin`。|
    |群集登录密码 |输入密码。|
    |Ssh 用户名 |保留预填充的值`sshuser`。|
    |Ssh 密码 |输入密码。|
    |Sql 管理员登录名 |保留预填充的值`sqluser`。|
    |Sql 管理员密码 |输入密码。|
    |_artifacts 位置 | 使用默认值（除非想要在其他位置使用自己的 bacpac 文件）。|
    |_artifacts 位置 Sas 令牌 |留空。|
    |Bacpac 文件的名称 |使用默认值（除非想要使用自己的 bacpac 文件）。|
    |位置 |使用默认值。|

    Azure SQL 服务器的名称将为`<ClusterName>dbserver`。 数据库名称将是`<ClusterName>db`。 默认存储帐户名称将是`e6qhezrh2pdqu`。

3. 选择“我同意上述条款和条件”。

4. 选择“购买”。 此时会出现一个标题为“为模板部署提交部署”的新磁贴。 创建群集和 SQL 数据库大约需要 20 分钟时间。

## <a name="run-sqoop-jobs"></a>运行 Sqoop 作业

HDInsight 可以使用各种方法运行 Sqoop 作业。 使用下表来确定哪种方法最适合，并按链接进行演练。

| **使用此方法**，如果想要... | ...**交互式** shell | ...**批处理** | ...从此**客户端操作系统** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux、Unix、Mac OS X 或 Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows（暂时） |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>限制

* 批量导出 - 在基于 Linux 的 HDInsight 上，用于将数据导出到 Microsoft SQL Server 或 Azure SQL 数据库的 Sqoop 连接器目前不支持批量插入。
* 批处理 - 在基于 Linux 的 HDInsight 上，如果在执行插入时使用 `-batch` 开关，Sqoop 将执行多次插入而不是批处理插入操作。

## <a name="next-steps"></a>后续步骤
现在已经学习了如何使用 Sqoop。 若要了解更多信息，请参阅以下文章：

* [将 Apache Hive 和 HDInsight 配合使用](../hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](../hdinsight-use-pig.md)
* [将数据上传到 HDInsight](../hdinsight-upload-data.md)：了解将数据上传到 HDInsight/Azure Blob 存储的其他方法。
