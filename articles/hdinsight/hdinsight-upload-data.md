---
title: 在 HDInsight 中上传 Apache Hadoop 作业的数据
description: 了解如何在 HDInsight 中上载和访问 Apache Hadoop 作业的数据。 使用 Azure 经典 CLI、Azure 存储资源管理器、Azure PowerShell、Hadoop 命令行或 Sqoop。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: e73a8a420c7591a45a62ba38123c6b3368e0f738
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190633"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>在 HDInsight 中上传 Apache Hadoop 作业的数据

HDInsight 通过 Azure 存储提供 Hadoop 分布式文件系统（HDFS），并 Azure Data Lake Storage。 此存储包括 Gen1 和 Gen2。 Azure 存储和 Data Lake Storage Gen1 和 Gen2 都设计为 HDFS 扩展。 它们允许 Hadoop 环境中的完整组件集直接对它所管理的数据进行操作。 Azure 存储、Data Lake Storage Gen1 和 Gen2 是不同的文件系统。 系统针对数据的存储和计算进行了优化。 若要了解使用 Azure 存储的优点，请参阅[将 Azure 存储与 HDInsight 配合使用](hdinsight-hadoop-use-blob-storage.md)。 另请参阅，[将 Data Lake Storage Gen1 与 hdinsight 配合使用](hdinsight-hadoop-use-data-lake-store.md)，并[将 Data Lake Storage Gen2 与 hdinsight 配合使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

## <a name="prerequisites"></a>先决条件

在开始下一步之前，请注意以下要求：

* 一个 Azure HDInsight 群集。 有关说明，请参阅[Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。
* 了解以下文章：
    * [将 Azure 存储与 HDInsight 配合使用](hdinsight-hadoop-use-blob-storage.md)
    * [将 Data Lake Storage Gen1 与 HDInsight 配合使用](hdinsight-hadoop-use-data-lake-store.md)
    * [将 Data Lake Storage Gen2 与 HDInsight 配合使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>将数据上传到 Azure 存储

### <a name="utilities"></a>实用程序

Microsoft 提供以下实用工具用于操作 Azure 存储：

| 工具 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure 门户](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop 命令](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Hadoop 命令仅在 HDInsight 群集上可用。 使用该命令只能将数据从本地文件系统载入 Azure 存储。  

### <a name="hadoop-command-line"></a>Hadoop 命令行

仅当数据已存在于群集头节点中时，才可以使用 Hadoop 命令行将数据存储到 Azure 存储 Blob。

若要使用 Hadoop 命令，必须先使用[SSH 或 PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)连接到头节点。

连接之后，可以使用以下语法将文件上传到存储。

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

例如： `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

由于 HDInsight 的默认文件系统在 Azure 存储中，因此 /example/data/data.txt 实际是在 Azure 存储中。 也可以将该文件表示为：

    wasbs:///example/data/data.txt

或

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

有关使用文件的其他 Hadoop 命令的列表，请参阅[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> 在 Apache HBase 群集上，写入数据为 256 KB 时会使用默认块大小。 虽然在使用 HBase Api 或 REST API 时可良好运行，但使用 `hadoop` 或 `hdfs dfs` 命令编写大于 ~12 GB 的数据会导致错误。 有关详细信息，请参阅[在 blob 上写入时的存储异常](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob)。

### <a name="graphical-clients"></a>图形客户端

某些应用程序还提供可配合 Azure 存储空间使用的图形界面。 下表是其中一些应用程序的列表：

| 客户端 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [用于 HDInsight 的 Microsoft Visual Studio 工具](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure 存储浏览器](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [适用于 Microsoft Azure 的 CloudBerry Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>将 Azure 存储装载为本地驱动器

请参阅[将 Azure 存储装载为本地驱动器](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)。

## <a name="upload-using-services"></a>使用服务上传

### <a name="azure-data-factory"></a>Azure 数据工厂

Azure 数据工厂服务是一种完全托管的服务，可用于编写数据：存储、处理和移动服务，并可实现精简、适应性和可靠的数据生产管道。

|存储类型|文档|
|----|----|
|Azure Blob 存储|[使用 Azure 数据工厂将数据复制到 Azure Blob 存储或从 Azure Blob 存储复制数据](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[使用 Azure 数据工厂向/从 Azure Data Lake Storage Gen1 复制数据](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[使用 Azure 数据工厂将数据加载到 Azure Data Lake Storage Gen2 中](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop 是一种为在 Hadoop 和关系数据库之间传输数据而设计的工具。 使用它从关系数据库管理系统（RDBMS）（如 SQL Server、MySQL 或 Oracle）中导入数据。 然后进入 Hadoop 分布式文件系统（HDFS）。 使用 MapReduce 或 Hive 转换 Hadoop 中的数据，然后将数据导出回 RDBMS。

有关详细信息，请参阅[将 Sqoop 与 HDInsight 配合使用](hadoop/hdinsight-use-sqoop.md)。

### <a name="development-sdks"></a>开发 SDK

还可以使用 Azure SDK 通过以下编程语言来访问 Azure 存储：

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

有关安装 Azure SDK 的详细信息，请参阅 [Azure 下载](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将数据导入 HDInsight，请阅读以下文章了解分析：

* [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [以编程方式提交 Apache Hadoop 作业](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
