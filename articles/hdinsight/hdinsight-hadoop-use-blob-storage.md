---
title: 从与 HDFS 兼容的 Azure 存储查询数据 - Azure HDInsight
description: 了解如何从 Azure 存储和 Azure Data Lake Storage 查询数据，以存储分析结果。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/01/2019
ms.openlocfilehash: d934568f09e62ad8c1b472583cbfee79d2c837f6
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936859"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>将 Azure 存储与 Azure HDInsight 群集配合使用

若要分析 HDInsight 群集中的数据，可以将数据存储在[Azure 存储](../storage/common/storage-introduction.md)中， [Azure Data Lake Storage 第 1](../data-lake-store/data-lake-store-overview.md)/代[Azure Data Lake Storage 第 2](../storage/blobs/data-lake-storage-introduction.md)代或结合使用。 使用这些存储选项，可以安全地删除用于计算的 HDInsight 群集，而不会丢失用户数据。

Apache Hadoop 支持默认文件系统的概念。 默认文件系统意指默认方案和授权。 它还可用于解析相对路径。 在 HDInsight 群集创建过程中，可以指定 Azure 存储中的 Blob 容器作为默认文件系统，或者借助 HDInsight 3.6，可以选择 Azure 存储或 Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 作为默认文件系统（有少数例外）。 有关对将 Data Lake Storage Gen 1 同时用作默认存储和链接存储的支持能力，请参阅 [HDInsight 群集的可用性](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters)。

本文介绍如何配合使用 Azure 存储和 HDInsight 群集。 若要了解 Data Lake Storage Gen 1 与 HDInsight 群集如何配合工作，请参阅[将 Azure Data Lake Storage 与 Azure HDInsight 群集配合使用](hdinsight-hadoop-use-data-lake-store.md)。 若要深入了解如何创建 HDInsight 群集，请参阅[在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

Azure 存储是一种稳健、通用的存储解决方案，它与 HDInsight 无缝集成。 HDInsight 可将 Azure 存储中的 Blob 容器用作群集的默认文件系统。 通过 Hadoop 分布式的文件系统 (HDFS) 界面，可以针对作为 Blob 存储的结构化或非结构化数据直接运行 HDInsight 中的整套组件。

> [!IMPORTANT]  
> 存储帐户类型 **BlobStorage** 仅可用作 HDInsight 群集的辅助存储器。

| 存储帐户类型 | 支持的服务 | 支持的性能层 | 支持的访问层 |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2（常规用途 v2）  | Blob     | 标准                    | 热、冷、存档\*   |
| 存储（常规用途 v1）   | Blob     | 标准                    | 不可用                    |
| BlobStorage                    | Blob     | 标准                    | 热、冷、存档\*   |

建议不要使用默认 blob 容器来存储业务数据。 良好的做法是每次使用之后删除默认 blob 容器以降低存储成本。 默认容器包含应用程序日志和系统日志。 请确保在删除该容器之前检索日志。

不支持为多个群集共享一个 blob 容器作为默认文件系统。

> [!NOTE]  
> 存档访问层是一个脱机层，其中包含几个小时的检索延迟，不建议用于 HDInsight。 有关详细信息，请参阅[存档访问层](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)。

如果选择在“选定网络”上通过“防火墙和虚拟网络”限制来保护存储帐户的安全，请务必启用例外“允许受信任的 Microsoft 服务...”，这样 HDInsight 就能访问存储帐户。

## <a name="hdinsight-storage-architecture"></a>HDInsight 存储体系结构

下图提供了使用 Azure 存储时的 HDInsight 存储体系结构的抽象视图：

![Hadoop 群集使用 HDFS API 来访问 Blob 存储中的数据并将其存储在 Blob 存储中](./media/hdinsight-hadoop-use-blob-storage/storage-architecture.png "HDInsight 存储体系结构")

HDInsight 提供对在本地附加到计算节点的分布式文件系统的访问。 可使用完全限定 URI 访问该文件系统，例如：

    hdfs://<namenodehost>/<path>

另外，HDInsight 支持访问在 Azure 存储中存储的数据。 语法为：

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

以下是将 Azure 存储帐户与 HDInsight 群集配合使用时的一些注意事项。

* **已连接到群集的存储帐户中的容器：** 由于在创建过程中帐户名称和密钥将与群集相关联，因此，对这些容器中的 Blob 具有完全访问权限。

* **未连接到群集的存储帐户中的公用容器或公用 Blob：** 你对容器中的 blob 具有只读权限。
  
> [!NOTE]  
> 利用公共容器，可以获得该容器中可用的所有 Blob 的列表以及容器元数据。 利用公共 Blob，仅在知道正确 URL 时才可访问 Blob。 有关详细信息，请参阅[管理对容器和 Blob 的访问](../storage/blobs/storage-manage-access-to-resources.md)。

* **未连接到群集的存储帐户中的专用容器：** 你无法访问这些容器中的 Blob，除非在提交 WebHCat 作业时定义了存储帐户。 本文后面对此做了解释。

创建进程中定义的存储帐户及其密钥存储在`%HADOOP_HOME%/conf/core-site.xml`群集节点上。 HDInsight 的默认行为是使用 core-site.xml 文件中定义的存储帐户。 可以使用 [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) 修改此设置。

多个 WebHCat 作业，包括 Apache Hive、MapReduce、Apache Hadoop 流和 Apache Pig，都可以带有存储帐户和元数据的说明。 （它目前对带有存储帐户的 Pig 有效，但对元数据无效。）有关详细信息，请参阅[将 HDInsight 群集与备用存储帐户和元存储配合使用](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 可用于结构化和非结构化数据。 Blob 容器将数据存储为键/值对，没有目录层次结构。 不过，可在键名称中使用斜杠字符 (/)，使其看起来像存储在目录结构中的文件。 例如，Blob 的键可以是 *input/log1.txt*。 不存在实际的 *input* 目录，但由于键名称中包含斜杠字符，因此使其看起来像一个文件路径。

## <a id="benefits"></a>Azure 存储的优点

在 Azure 区域中的存储帐户资源附近创建计算群集的方式使计算群集不归置计算群集和存储资源的隐含性能成本得到了缓解，在这种情况下，高速网络使用于访问 Azure 存储中的数据的计算节点。

在 Azure 存储而非 HDFS 中存储数据有几个好处：

* **数据重用和共享：** HDFS 中的数据位于计算群集内。 仅有权访问计算群集的应用程序才能通过 HDFS API 使用数据。 可以通过 HDFS Api 或[Blob 存储 REST api](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)访问 Azure 存储中的数据。 因此，可使用大量应用程序（包括其他 HDInsight 群集）和工具来生成和使用此类数据。

* **数据存档：** 通过在 Azure 存储中存储数据，可以安全地删除用于计算的 HDInsight 群集而不会丢失用户数据。

* **数据存储成本：** 与在 Azure 存储中存储数据相比，在 DFS 中长期存储数据的成本更高，因为计算群集的成本高于 Azure 存储的成本。 此外，因为无需在每次生成计算群集时重新加载数据，所以还会保存数据加载成本。

* **弹性横向扩展：** 尽管 HDFS 提供了横向扩展文件系统，但具体的扩展由你为群集创建的节点数量决定。 与依靠自动获得的 Azure 存储的弹性缩放功能相比，更改缩放的过程可能会更复杂。

* **异地复制：** 可对 Azure 存储进行异地复制。 尽管这可提供地理恢复和数据冗余功能，但针对异地复制位置的故障转移将大大影响性能，并且可能会产生额外成本。 因此，我们建议你仅在数据的价值值得你支付额外成本时才选择适当的异地复制。

某些 MapReduce 作业和包可能会产生中间结果，并不想在 Azure 存储中存储这些结果。 在此情况下，仍可以选择将数据存储在本地 HDFS 中。 实际上，HDInsight 在 Hive 作业和其他过程中会为其中某些中间结果使用 DFS。

> [!NOTE]  
> 大多数 HDFS 命令（例如 `ls`、`copyFromLocal` 和 `mkdir`）仍按预期工作。 只有特定于本机 HDFS 实现（称作 DFS）的命令在 Azure 存储上会显示不同的行为，例如 `fschk` 和 `dfsadmin`。

## <a name="address-files-in-azure-storage"></a>确定 Azure 存储中文件的地址

用于从 HDInsight 访问 Azure 存储中的文件的 URI 方案为：

```config
wasbs://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

URI 方案提供了使用 *wasb:* 前缀的未加密访问和使用 *wasbs* 的 SSL 加密访问。 建议尽量使用 *wasbs* ，即使在访问位于同一 Azure 区域内的数据时也是如此。

`<BlobStorageContainerName>` 标识 Azure 存储中 Blob 容器的名称。
`<StorageAccountName>` 标识 Azure 存储帐户名称。 完全限定域名 (FQDN) 是必需的。

如果 `<BlobStorageContainerName>` 和 `<StorageAccountName>` 均未指定，则使用默认文件系统。 对于默认文件系统中的文件，可以使用相对路径或绝对路径。 例如，可以使用以下任一方式引用随 HDInsight 群集提供的 *hadoop-mapreduce-examples.jar* 文件：

```config
wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasbs:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> 在 HDInsight 版本 2.1 和 1.6 群集中，文件名为 `hadoop-examples.jar`。

path 是文件或目录 HDFS 路径名。 由于 Azure 存储中的容器是键值存储，因此没有真正的分层文件系统。 Blob 键中的斜杠字符 (/) 解释为目录分隔符。 例如， *hadoop-mapreduce-examples.jar* 的 Blob 名称是：

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> 在 HDInsight 外部使用 Blob 时，大多数实用程序无法识别 WASB 格式，应该改用基本的路径格式，如 `example/jars/hadoop-mapreduce-examples.jar`。

## <a name="blob-containers"></a>Blob 容器

若要使用 Blob，请先创建 [Azure 存储帐户](../storage/common/storage-create-storage-account.md)。 在此过程中，可指定在其中创建存储帐户的 Azure 区域。 群集和存储帐户必须位于同一区域。 Hive 元存储 SQL Server 数据库和 Apache Oozie 元存储 SQL Server 数据库也必须位于同一区域。

无论所创建的每个 Blob 位于何处，它都属于 Azure 存储帐户中的某个容器。 此容器可以是在 HDInsight 外部创建的现有的 Blob，也可以是为 HDInsight 群集创建的容器。

默认的 Blob 容器存储群集特定的信息，如作业历史记录和日志。 请不要多个 HDInsight 群集之间共享默认的 Blob 容器。 这可能会损坏作业历史记录。 建议为每个群集使用不同的容器，并将共享数据放在所有相关群集的部署中指定的链接存储帐户，而不是默认的存储帐户中。 有关配置链接存储帐户的详细信息，请参阅[创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)。 但是，在删除原始的 HDInsight 群集后，可以重用默认存储容器。 对于 HBase 群集，实际上可以通过使用已删除的 HBase 群集使用的默认 blob 容器创建新的 HBase 群集来保留 HBase 表架构和数据。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>与 Azure 存储交互

Microsoft 提供以下用于操作 Azure 存储的工具：

| Tool | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure 门户](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>使用其他存储帐户

创建 HDInsight 群集时，可以指定要与其关联的 Azure 存储帐户。 除了此存储帐户外，在创建过程中或群集创建完成后，还可以从同一 Azure 订阅或不同 Azure 订阅添加其他存储帐户。 有关添加其他存储帐户的说明，请参阅[创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)。

> [!WARNING]  
> 不支持在 HDInsight 群集之外的其他位置使用别的存储帐户。

## <a name="next-steps"></a>后续步骤

本文介绍如何将与 HDFS 兼容的 Azure 存储和 HDInsight 配合使用。 这样可以生成可缩放的长期存档数据采集解决方案，并使用 HDInsight 来解锁所存储结构化和非结构化数据内的信息。

有关详细信息，请参阅：

* [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure Data Lake Storage 入门](../data-lake-store/data-lake-store-get-started-portal.md)
* [将数据上传到 HDInsight](hdinsight-upload-data.md)
* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](hadoop/hdinsight-use-pig.md)
* [使用 Azure 存储共享访问签名来限制使用 HDInsight 访问数据](hdinsight-storage-sharedaccesssignature-permissions.md)
* [将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](hdinsight-hadoop-use-data-lake-storage-gen2.md)
