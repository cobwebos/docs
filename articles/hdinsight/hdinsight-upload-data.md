---
title: 在 HDInsight 中上传 Apache Hadoop 作业的数据
description: 了解如何上传和访问 HDInsight 中的 Apache Hadoop 作业的数据。 使用 Azure 经典 CLI、Azure 存储资源管理器、Azure PowerShell、Hadoop 命令行或 Sqoop。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017
ms.date: 04/07/2020
ms.openlocfilehash: c862633245e75613f9e4f9956486f872b96239f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875004"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>在 HDInsight 中上传 Apache Hadoop 作业的数据

HDInsight 通过 Azure 存储和 Azure 数据湖存储提供 Hadoop 分布式文件系统 （HDFS）。 此存储包括第 1 代和第 2 代。 Azure 存储和数据存储湖存储第 1 代和第 2 代设计为 HDFS 扩展。 它们使 Hadoop 环境中的完整组件集能够直接基于其管理的数据进行操作。 Azure 存储、数据存储第 1 代和第 2 代是不同的文件系统。 系统经过优化，可存储数据并计算该数据。 若要了解使用 Azure 存储的优点，请参阅[将 Azure 存储与 HDInsight 配合使用](hdinsight-hadoop-use-blob-storage.md)。 另请参阅，[使用数据湖存储第 1 代与 HDInsight](hdinsight-hadoop-use-data-lake-store.md)， 并使用[数据存储湖存储 Gen2 与 HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>先决条件

在开始下一步之前，请注意以下要求：

* 一个 Azure HDInsight 群集。 有关说明，请参阅 [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)或[创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)。
* 了解以下文章：
    * [将 Azure 存储与 HDInsight 一起使用](hdinsight-hadoop-use-blob-storage.md)
    * [将 Data Lake Storage Gen1 与 HDInsight 配合使用](hdinsight-hadoop-use-data-lake-store.md)
    * [将 Data Lake Storage Gen2 与 HDInsight 配合使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>将数据上传到 Azure 存储

## <a name="utilities"></a>实用程序

Microsoft 提供以下实用工具用于操作 Azure 存储：

| 工具 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure 门户](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [阿兹比贝](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop 命令](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Hadoop 命令仅在 HDInsight 群集上可用。 使用该命令只能将数据从本地文件系统载入 Azure 存储。  

## <a name="hadoop-command-line"></a>Hadoop 命令行

仅当数据已存在于群集头节点中时，才可以使用 Hadoop 命令行将数据存储到 Azure 存储 Blob。

要使用 Hadoop 命令，必须首先使用[SSH 或 PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)连接到头节点。

连接之后，可以使用以下语法将文件上传到存储。

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

例如，`hadoop fs -copyFromLocal data.txt /example/data/data.txt`

由于 HDInsight 的默认文件系统在 Azure 存储中，因此 /example/data/data.txt 实际是在 Azure 存储中。 也可以将该文件表示为：

    wasbs:///example/data/data.txt

or

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

有关处理文件的其他 Hadoop 命令的列表，请参阅[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> 在 Apache HBase 群集上，写入数据为 256 KB 时会使用默认块大小。 虽然在使用 HBase Api 或 REST API 时可良好运行，但使用 `hadoop` 或 `hdfs dfs` 命令编写大于 ~12 GB 的数据会导致错误。 有关详细信息，请参阅本文的[在 Blob 上编写时的存储异常](#storage-exception-for-write-on-blob)部分。

## <a name="graphical-clients"></a>图形客户端

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

Azure 数据工厂服务是一种完全托管的服务，用于将数据组合：将存储、处理和移动服务组合到简化、适应性强和可靠的数据生产管道中。

|存储类型|文档|
|----|----|
|Azure Blob 存储|[使用 Azure 数据工厂将数据复制到或从 Azure Blob 存储中复制数据](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[使用 Azure 数据工厂向/从 Azure Data Lake Storage Gen1 复制数据](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[使用 Azure 数据工厂将数据加载到 Azure Data Lake Storage Gen2 中](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop 是一种为在 Hadoop 和关系数据库之间传输数据而设计的工具。 使用它从关系数据库管理系统 （RDBMS） 导入数据，例如 SQL Server、MySQL 或 Oracle。 然后进入 Hadoop 分布式文件系统 （HDFS）。 使用 MapReduce 或 Hive 转换 Hadoop 中的数据，然后将数据导出回 RDBMS。

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

## <a name="troubleshooting"></a>疑难解答

### <a name="storage-exception-for-write-on-blob"></a>在 Blob 上编写时的存储异常

**症状**： 当`hadoop`使用`hdfs dfs`或 命令在 HBase 群集上写入 +12 GB 或更大的文件时，可能会遇到以下错误：

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**原因**：写入 Azure 存储时，HDInsight 群集上的 HBase 默认为 256 KB 的块大小。 尽管这对 HBase API 或 REST API 来说可良好运行，但使用 `hadoop` 或 `hdfs dfs` 命令行实用工具时则会导致错误。

**解决方法**：使用 `fs.azure.write.request.size` 指定更大的块大小。 您可以使用 参数在每次使用的基础上`-D`执行此修改。 以下命令是将此参数用于 `hadoop` 命令的示例：

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

还可使用 Apache Ambari 以全局方式增加 `fs.azure.write.request.size` 的值。 可使用以下步骤在 Ambari Web UI 中更改该值：

1. 在浏览器中，转到群集的 Ambari Web UI。 URL 是`https://CLUSTERNAME.azurehdinsight.net`，`CLUSTERNAME`群集的名称位于此位置。 出现提示时，输入群集的管理员名称和密码。
2. 在屏幕左侧选择“HDFS”****，并选择“配置”**** 选项卡。
3. 在“筛选...”**** 字段中输入 `fs.azure.write.request.size`。
4. 将值从 262144 (256 KB) 更改为新的值。 例如，4194304 (4 MB)。

    ![通过 Ambari Web UI 更改值的图像](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

有关如何使用 Ambari 的详细信息，请参阅[使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)。

## <a name="next-steps"></a>后续步骤

现在，您已经了解如何将数据输入 HDInsight，请阅读以下文章以了解分析：

* [开始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [以编程方式提交 Apache Hadoop 作业](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](./use-pig.md)
