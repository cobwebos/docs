---
title: 将 HDFS CLI 与 Azure Data Lake Storage Gen2 预览版配合使用
description: 适用于 Azure Data Lake Storage Gen2 预览版的 HDFS CLI 简介
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: b4485e234e19e93a852895c80775b8aadc7a15ce
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976279"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>将 HDFS CLI 与 Data Lake Storage Gen2 配合使用

使用 Azure Data Lake Storage Gen2 预览版，可以像使用 [Hadoop 分布式文件系统 (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) 一样管理和访问数据。 无论你是附加 HDInsight 群集还是使用 Azure Databricks 运行 Apache Spark 作业来对 Azure 存储帐户中存储的数据执行分析，都可以使用命令行接口 (CLI) 来检索和操作所加载的数据。

## <a name="hdfs-cli-with-hdinsight"></a>将 HDFS CLI 与 HDInsight 配合使用

HDInsight 提供对在本地附加到计算节点的分布式文件系统的访问。 可以使用直接与 HDFS 以及与 Hadoop 支持的其他文件系统进行交互的 shell 来访问此文件系统。 下面是常用的命令和有用资源的链接。

>[!IMPORTANT]
>创建群集后便开始 HDInsight 群集计费，删除群集后停止计费。 群集以每分钟按比例收费，因此无需再使用群集时，应始终将其删除。 若要了解如何删除群集，请参阅我们的[有关该主题的文章](../../hdinsight/hdinsight-delete-cluster.md)。 但是，即使删除了 HDInsight 群集，在启用了 Data Lake Storage Gen2 的存储帐户中存储的数据仍然会保留。

### <a name="get-a-list-of-files-or-directories"></a>获取文件或目录列表

    hdfs dfs -ls <args>

### <a name="create-a-directory"></a>创建目录

    hdfs dfs -mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>删除文件或目录

    hdfs dfs -rm [-skipTrash] URI [URI ...]

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>在 Linux 上结合使用 HDFS CLI 和 HDInsight Hadoop 群集

首先建立[对服务的远程访问](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services)。 如果选择了 [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)，则示例 PowerShell 代码将如下所示：

```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
可以在 Azure 门户中 HDInsight 群集边栏选项卡的“SSH + 群集登录”部分中找到连接字符串。 SSH 凭据是在创建群集时指定的。

有关 HDFS CLI 的详细信息，请参阅[官方文档](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)和 [HDFS 权限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)。 有关 Databricks 中的 ACL 的详细信息，请参阅[机密 CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli)。 

## <a name="hdfs-cli-with-azure-databricks"></a>将 HDFS CLI 与 Azure Databricks 配合使用

Databricks 提供了基于 Databricks REST API 构建的易于使用的 CLI。 此开放源代码项目承载在 [GitHub](https://github.com/databricks/databricks-cli) 上。 下面是常用的命令。

### <a name="get-a-list-of-files-or-directories"></a>获取文件或目录列表

    dbfs ls [-l]

### <a name="create-a-directory"></a>创建目录

    dbfs mkdirs

### <a name="delete-a-file"></a>删除文件

    dbfs rm [-r]

与 Databricks 进行交互的另一种方式是使用 Notebook。 虽然 Notebook 有一种主语言，但你可以通过在单元的开头指定语言魔法命令 %language 来混合使用多种语言。 具体而言，%sh 允许在 Notebook 中执行 shell 代码，很像本文上文中的 HDInsight 示例。

### <a name="get-a-list-of-files-or-directories"></a>获取文件或目录列表

    %sh ls <args>

### <a name="create-a-directory"></a>创建目录

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>删除文件或目录

    %sh rm [-skipTrash] URI [URI ...]

在 Azure Databricks 中启动 Spark 群集后，你将创建一个新的 Notebook。 示例 Notebook 脚本将如下所示：

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

有关 Databricks CLI 的详细信息，请参阅[官方文档](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)。 有关 Notebook 的详细信息，请参阅该文档的 [notebooks](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) 部分。

## <a name="set-file-and-directory-level-permissions"></a>设置文件和目录级别权限

设置并获取文件和目录级别访问权限。 以下命令可帮助你开始使用。 

若要详细了解 Azure Data Lake Gen2 文件系统的文件和目录级别权限，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](storage-data-lake-storage-access-control.md)。

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>显示文件和目录的访问控制列表 (ACL)

    hdfs dfs -getfacl [-R] <path>

示例：

`hdfs dfs -getfacl -R /dir`

请参阅 [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>设置文件和目录的 ACL

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

示例：

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

请参阅 [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>更改文件的所有者

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

请参阅 [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>更改文件的组关联

    hdfs dfs -chgrp [-R] <group> <URI>

请参阅 [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>更改文件的权限

    hdfs dfs -chmod [-R] <mode> <URI>

请参阅 [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

若要查看命令的完整列表，请访问 [Apache Hadoop 2.4.1 文件系统 Shell 指南](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)网站。

## <a name="next-steps"></a>后续步骤

[在 Azure Databricks 中使用支持 Azure Data Lake Storage Gen2 的帐户](./data-lake-storage-quickstart-create-databricks-account.md) 