---
title: 使用 DistCp 将数据从 WASB 复制到 Azure Data Lake Storage Gen1
description: 使用 DistCp 工具将数据复制到 Azure 存储 blob 并将其从 Azure 存储 blob 复制到 Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638827"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>使用 DistCp 在 Azure 存储 blob 和 Azure Data Lake Storage Gen1 之间复制数据

> [!div class="op_single_selector"]
> * [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

如果有权访问 Azure Data Lake Storage Gen1 的 HDInsight 群集，则可以使用 Hadoop 生态系统工具（如 DistCp）将数据从 HDInsight 群集存储（WASB）复制到 Data Lake Storage Gen1 帐户。 本文介绍如何使用 DistCp 工具。

## <a name="prerequisites"></a>必备组件

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)。
* 具有 Data Lake Storage Gen1 帐户访问权限的 Azure HDInsight 群集。 请参阅[创建包含 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>从 HDInsight Linux 群集使用 DistCp

An HDInsight 群集随附了 DistCp 工具，可用于将数据从不同源复制到 HDInsight 群集。 如果已将 HDInsight 群集配置为使用 Data Lake Storage Gen1 作为附加存储，则可以使用 DistCp 来向 Data Lake Storage Gen1 帐户复制数据。 本部分介绍如何使用 DistCp 工具。

1. 从桌面使用 SSH 连接到群集。 请参阅[连接到基于 Linux 的 HDInsight 群集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。 通过 SSH 提示符运行命令。

1. 验证是否可以访问 Azure 存储 blob （WASB）。 运行以下命令：

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   输出在存储 blob 中提供了一个内容列表。

1. 同样，验证是否可从此群集访问 Data Lake Storage Gen1 帐户。 运行以下命令：

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    输出提供 Data Lake Storage Gen1 帐户中的文件和文件夹的列表。

1. 使用 DistCp 将数据从 WASB 复制到 Data Lake Storage Gen1 帐户。

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    该命令会将 WASB 中 /example/data/gutenberg/ 文件夹的内容复制到 Data Lake Storage Gen1 帐户中的 /myfolder。

1. 同样，使用 DistCp 将数据从 Data Lake Storage Gen1 帐户复制到 WASB。

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    命令将 Data Lake Storage Gen1 帐户中的 **/myfolder**的内容复制到 WASB 中的 **/example/data/gutenberg/** 文件夹。

## <a name="performance-considerations-while-using-distcp"></a>使用 DistCp 时的性能注意事项

由于 DistCp 工具的最小粒度是一个文件，因此设置最大同步副本数是最重要的参数，用于根据 Data Lake Storage Gen1 对其进行优化。 可以通过在命令行上设置映射器（m '）参数的数目来控制同时副本的数目。 此参数指定用于复制数据的映射器的最大数目。 默认值为 20。

示例：

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>如何确定要使用的映射器数

请参考下面的指导。

* **步骤 1：确定总 YARN 内存** - 第一步是确定可供运行 DistCp 作业的群集使用的 YARN 内存。 可在与群集关联的 Ambari 门户中获取此信息。 导航到 YARN 并**查看 "配置" 选项卡**以查看 YARN 内存。 要获取总 YARN 内存，可将每个节点的 YARN 内存与你在群集中拥有的节点数相乘。

* **步骤 2：计算映射器数** - **m** 的值等于总 YARN 内存除以 YARN 容器大小的商。 YARN 容器大小信息也可在 Ambari 门户中使用。 导航到 YARN 并**查看 "配置" 选项卡**。YARN 容器大小显示在此窗口中。 要到达映射器（**m**）数的公式为：

   `m = (number of nodes * YARN memory for each node) / YARN container size`

示例：

假设群集中有四个 D14v2s 节点，并且想要从10个不同的文件夹传输 10 TB 的数据。 每个文件夹都包含不同数量的数据，并且每个文件夹中的文件大小也不同。

* 总 YARN 内存 - 从 Ambari 门户确定一个 D14 节点的 YARN 内存为 96 GB。 因此，具有 4 个节点的群集的总 YARN 内存是：

   `YARN memory = 4 * 96GB = 384GB`

* 映射器数 - 从 Ambari 门户确定一个 D14 群集节点的 YARN 容器大小为 3072。 因此，映射器数为：

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

如果其他应用程序正在使用内存，则可以选择仅使用部分群集的 YARN 内存用于 DistCp。

### <a name="copying-large-datasets"></a>复制大型数据集

如果要移动的数据集的大小较大（例如，> 1 TB），或者如果有许多不同的文件夹，请考虑使用多个 DistCp 作业。 这可能不会提高性能，但会传播作业，因此，如果任何作业失败，只需重新启动该特定作业（而不是整个作业）。

### <a name="limitations"></a>限制

* DistCp 会尝试创建大小类似的映射器以优化性能。 增加映射器数不一定始终会提高性能。

* DistCp 被限制为每个文件只有一个映射器。 因此，你不应拥有比文件更多的映射器。 由于 DistCp 只能将一个映射器分配给一个文件，这会限制可用于复制大型文件的并发量。

* 如果有少量的大型文件，请将它们拆分为 256 MB 的文件块，以便更好地进行并发。

* 如果要从 Azure Blob 存储帐户进行复制，则可在 Blob 存储端限制复制作业。 这会降低复制作业的性能。 若要了解有关 Azure Blob 存储的限制的详细信息，请参阅 azure[订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md)中的 azure 存储限制。

## <a name="see-also"></a>另请参阅

* [将数据从 Azure 存储 blob 复制到 Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
* [将 Azure Data Lake Analytics 与 Data Lake Storage Gen1 配合使用](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)
