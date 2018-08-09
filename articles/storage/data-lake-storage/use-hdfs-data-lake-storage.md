---
title: 将 HDFS CLI 与 Azure Data Lake Storage Gen2 预览版配合使用
description: 适用于 Azure Data Lake Storage Gen2 预览版的 HDFS CLI 简介
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: ef28468c7aa420c145f5e6d79e2b079bd1d094f4
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523676"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>将 HDFS CLI 与 Data Lake Storage Gen2 配合使用

使用 Azure Data Lake Storage Gen2 预览版，可以像使用 [Hadoop 分布式文件系统 (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) 一样管理和访问数据。 无论你是附加 HDInsight 群集还是使用 Azure Databricks 运行 Apache Spark 作业来对 Azure Data Lake Storage Gen2 中存储的数据执行分析，都可以使用命令行接口 (CLI) 来检索和操作所加载的数据。 本文的剩余内容概述了在 [Azure 存储团队致力于增加对 Azure 存储资源管理器和 Azure 门户的支持](https://azure.microsoft.com/roadmap/)时可供你选择的选项。

## <a name="hdfs-cli-with-hdinsight"></a>将 HDFS CLI 与 HDInsight 配合使用

HDInsight 提供对在本地附加到计算节点的分布式文件系统的访问。 可以使用直接与 HDFS 以及与 Hadoop 支持的其他文件系统进行交互的 shell 来访问此文件系统。 下面是常用的命令和有用资源的链接。

>[!IMPORTANT]
>创建群集后便开始 HDInsight 群集计费，删除群集后停止计费。 群集以每分钟为单位按比例收费，因此无需再使用群集时，应始终将其删除（了解如何[删除群集](../../hdinsight/hdinsight-delete-cluster.md)）。 但是，即使删除了 HDInsight 群集，Azure Data Lake Storage Gen2 中存储的数据仍然会保留。

若要获取文件或目录的列表，请使用以下命令：

    hdfs dfs -ls <args>
若要创建目录，请使用以下命令：

    hdfs dfs -mkdir [-p] <paths>
若要删除文件或目录，请使用以下命令：

    hdfs dfs -rm [-skipTrash] URI [URI ...]


现在，让我们以 Linux 上的 HDInsight Hadoop 群集为例进行说明。 若要使用 HDFS CLI，首选需要建立[对服务的远程访问](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services)。 如果选择了 [SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)，则示例 PowerShell 代码将如下所示：
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

可以在 Azure 门户中 HDInsight 群集边栏选项卡的“SSH + 群集登录”部分中找到连接字符串。 SSH 凭据是在创建群集时指定的。

有关 HDFS CLI 的详细信息，请参阅[官方文档](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)和 [HDFS 权限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)。

## <a name="hdfs-cli-with-azure-databricks"></a>将 HDFS CLI 与 Azure Databricks 配合使用

Databricks 提供了基于 Databricks REST API 构建的易于使用的 CLI。 此开放源代码项目承载在 [GitHub](https://github.com/databricks/databricks-cli) 上。 下面是常用的命令。

若要获取文件或目录的列表，请使用以下命令：

    dbfs ls [-l]
若要创建目录，请使用以下命令：

    dbfs mkdirs
若要删除文件，请使用以下命令：

    dbfs rm [-r]

与 Databricks 进行交互的另一种方式是使用 Notebook。 虽然 Notebook 有一种主语言，但你可以通过在单元的开头指定语言魔法命令 %language 来混合使用多种语言。 具体而言，%sh 允许在 Notebook 中执行 shell 代码，很像本文上文中的 HDInsight 示例。

若要获取文件或目录的列表，请使用以下命令：

    %sh ls <args>
若要创建目录，请使用以下命令：

    %sh mkdir [-p] <paths>
若要删除文件或目录，请使用以下命令：

    %sh rm [-skipTrash] URI [URI ...]

在 Azure Databricks 中启动 Spark 群集后，你将创建一个新的 Notebook。 示例 Notebook 脚本将如下所示：

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

有关 Databricks CLI 的详细信息，请参阅[官方文档](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)。 有关 Notebook 的详细信息，请参阅该文档的 [notebooks](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) 部分。

## <a name="next-steps"></a>后续步骤

- [创建采用 Azure Data Lake Storage Gen2 的 HDInsight 群集](./quickstart-create-connect-hdi-cluster.md)
- [在 Azure Databricks 中使用支持 Azure Data Lake Storage Gen2 的帐户](./quickstart-create-databricks-account.md) 