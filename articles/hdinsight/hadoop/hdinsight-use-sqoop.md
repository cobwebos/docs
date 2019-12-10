---
title: 通过 Azure HDInsight (Apache Hadoop) 运行 Apache Sqoop 作业
description: 了解如何使用工作站中的 Azure PowerShell 在 Hadoop 群集与 Azure SQL 数据库之间运行 Sqoop 导入和导出。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951847"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>在 HDInsight 中将 Apache Sqoop 与 Hadoop 配合使用

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何在 hdinsight 中使用 Apache Sqoop 在 HDInsight 群集和 Azure SQL 数据库之间导入和导出数据。

尽管 Apache Hadoop 是处理非结构化和半结构化数据（如日志和文件）的自然选择，但可能还需要处理存储在关系数据库中的结构化数据。

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html)是一种用于在 Hadoop 群集和关系数据库之间传输数据的工具。 可以使用此工具将数据从关系数据库管理系统 (RDBMS)（如 SQL Server、MySQL 或 Oracle）中导入到 Hadoop 分布式文件系统 (HDFS)，在 Hadoop 中使用 MapReduce 或 Apache Hive 转换数据，然后将数据导回 RDBMS。 本文使用的是关系数据库的 SQL Server 数据库。

> [!IMPORTANT]  
> 本文将设置测试环境以执行数据传输。 然后，从 "[运行 Sqoop 作业](#run-sqoop-jobs)" 一节中的其中一种方法为此环境选择数据传输方法。

有关 HDInsight 群集上支持的 Sqoop 版本，请参阅[hdinsight 提供的群集版本有哪些新功能？](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>了解方案

HDInsight 群集带有某些示例数据。 可使用以下两个示例：

* Apache Log4j 日志文件，位于 `/example/data/sample.log`。 以下日志会从该文件中提取出来：

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* 名为 `hivesampletable`的 Hive 表，它引用位于 `/hive/warehouse/hivesampletable`的数据文件。 该表包含一些移动设备数据。
  
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

本文介绍如何使用这两个数据集来测试 Sqoop 导入和导出。

## <a name="create-cluster-and-sql-database"></a>设置测试环境

通过使用 Azure 资源管理器模板 Azure 门户创建群集、SQL 数据库和其他对象。 可以在[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)中找到该模板。 资源管理器模板调用 bacpac 包以将表架构部署到 SQL 数据库。  bacpac 包位于公共 blob 容器 https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac 中。 如果想要为 bacpac 文件使用私有容器，请使用模板中的以下值：

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> 使用模板或 Azure 门户进行的导入操作仅支持从 Azure Blob 存储导入 BACPAC 文件。

1. 选择下图以打开 Azure 门户中的资源管理器模板。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. 输入以下属性：

    |字段 |Value |
    |---|---|
    |Subscription |从下拉列表中选择你的 Azure 订阅。|
    |Resource group |从下拉列表中选择资源组，或创建一个新的资源组|
    |Location |从下拉列表中选择一个区域。|
    |群集名称 |输入 Hadoop 群集的名称。 仅使用小写字母。|
    |群集登录用户名 |保留预填充值 `admin`。|
    |群集登录密码 |输入密码。|
    |Ssh 用户名 |保留预填充值 `sshuser`。|
    |Ssh 密码 |输入密码。|
    |Sql 管理员登录名 |保留预填充值 `sqluser`。|
    |Sql 管理员密码 |输入密码。|
    |_artifacts 位置 | 如果要在其他位置使用自己的 bacpac 文件，请使用默认值。|
    |_artifacts 位置 Sas 令牌 |留空。|
    |Bacpac 文件名 |使用默认值，除非你想要使用自己的 bacpac 文件。|
    |Location |使用默认值。|

    将 `<ClusterName>dbserver`Azure SQL Server 名称。 数据库名称将 `<ClusterName>db`。 默认存储帐户名称将 `e6qhezrh2pdqu`。

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

* 大容量导出-对于基于 Linux 的 HDInsight，用于将数据导出到 Microsoft SQL Server 或 Azure SQL 数据库的 Sqoop 连接器目前不支持批量插入。
* 批处理 - 在基于 Linux 的 HDInsight 上，如果在执行插入时使用 `-batch` 开关，Sqoop 将执行多次插入而不是批处理插入操作。

## <a name="next-steps"></a>后续步骤

现在，你已学习了如何使用 Sqoop。 若要了解更多信息，请参阅以下文章：

* [将 Apache Hive 和 HDInsight 配合使用](../hdinsight-use-hive.md)
* [将数据上传到 HDInsight](../hdinsight-upload-data.md)：了解将数据上传到 HDInsight/Azure Blob 存储的其他方法。
* [使用 Apache Sqoop 在 Apache Hadoop on HDInsight 与 SQL 数据库之间导入和导出数据](./apache-hadoop-use-sqoop-mac-linux.md)