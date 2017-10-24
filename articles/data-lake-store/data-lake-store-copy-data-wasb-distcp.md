---
title: "使用 Distcp 将数据复制到 WASB 和从 WASB 复制到 Data Lake Store | Microsoft Docs"
description: "使用 Distcp 工具将数据复制到 Azure 存储 Blob 和从 Azure 存储 Blob 复制到 Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: 1c9e100b4a0e7781f0782a49835d50492895ded1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>使用 Distcp 在 Azure 存储 Blob 和 Data Lake Store 之间复制数据
> [!div class="op_single_selector"]
> * [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

如果拥有具有 Data Lake Store 访问权限的 HDInsight 群集，则可使用 Distcp 等 Hadoop 生态系统工具将数据复制到 HDInsight 群集存储 (WASB) 和从 HDInsight 群集存储 (WASB) 复制到 Data Lake Store 帐户。 本文提供如何使用 Distcp 工具的说明。

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Store 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)
* 具有 Data Lake Store 帐户访问权限的**Azure HDInsight 群集**。 请参阅[创建包含 Data Lake Store 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。

## <a name="do-you-learn-fast-with-videos"></a>通过视频学得更快？
[观看此视频](https://mix.office.com/watch/1liuojvdx6sie)，了解如何使用 DistCp 在 Azure 存储 Blob 和 Data Lake Store 之间复制数据。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>从 HDInsight Linux 群集使用 Distcp

HDInsight 群集附带 Distcp 实用工具，该实用工具可用于从不同源中复制数据到 HDInsight 群集。 如果已配置 HDInsight 群集将 Data Lake Store 用作其他存储，则 Distcp 实用工具也可独立用于向/从 Data Lake Store 帐户复制数据。 本部分介绍如何使用 Distcp 实用工具。

1. 从桌面使用 SSH 连接到群集。 请参阅[连接到基于 Linux 的 HDInsight 群集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。 通过 SSH 提示符运行命令。

2. 验证是否可访问 Azure 存储 Blob (WASB)。 运行以下命令：

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    输出会提供存储 blob 中内容的列表。

3. 同样，验证是否可从此群集访问 Data Lake Store 帐户。 运行以下命令：

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    输出会提供 Data Lake Store 帐户中文件/文件夹的列表。

4. 使用 Distcp 从 WASB 复制数据到 Data Lake Store 帐户。

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    该命令会将 WASB 中 /example/data/gutenberg/ 文件夹的内容复制到 Data Lake Store 帐户中的 /myfolder。

5. 同样，使用 Distcp 从 Data Lake Store 帐户复制数据到 WASB。

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    该命令会将 Data Lake Store 帐户中 /myfolder 的内容复制到 WASB 中的 /example/data/gutenberg/ 文件夹。

## <a name="performance-considerations-while-using-distcp"></a>使用 DistCp 时的性能注意事项

由于 DistCp 的最小粒度是单个文件，设置同步复制的最大数目是针对 Data Lake Store 对其进行优化的最重要参数。 同步复制的数目可通过在命令行上设置映射器数（“m”）参数进行控制。 此参数指定用于复制数据的映射器的最大数目。 默认值为 20。

**示例**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>如何确定要使用的映射器数？

请参考下面的指导。

* **步骤 1：确定总 YARN 内存** - 第一步是确定可供运行 DistCp 作业的群集使用的 YARN 内存。 可在与群集关联的 Ambari 门户中获取此信息。 导航到 YARN 并查看“配置”选项卡可看到 YARN 内存。 要获取总 YARN 内存，可将每个节点的 YARN 内存与你在群集中拥有的节点数相乘。

* **步骤 2：计算映射器数** - **m** 的值等于总 YARN 内存除以 YARN 容器大小的商。 YARN 容器大小的信息也可在 Ambari 门户中找到。 导航到 YARN 并查看“配置”选项卡。YARN 容器大小显示在此窗口中。 用于得到映射器数 (**m**) 的公式是

        m = (number of nodes * YARN memory for each node) / YARN container size

**示例**

假设你在群集中有 4 个 D14v2s 节点，并且想要从 10 个不同的文件夹传输 10 TB 的数据。 每个文件夹都包含不同数量的数据，并且每个文件夹中的文件大小也不同。

* 总 YARN 内存 - 从 Ambari 门户确定一个 D14 节点的 YARN 内存为 96 GB。 因此，具有 4 个节点的群集的总 YARN 内存是： 

        YARN memory = 4 * 96GB = 384GB

* 映射器数 - 从 Ambari 门户确定一个 D14 群集节点的 YARN 容器大小为 3072。 因此，映射器数为：

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

如果其他应用程序正在使用内存，则可以选择仅将群集的部分 YARN 内存用于 DistCp。

### <a name="copying-large-datasets"></a>复制大型数据集

当要移动的数据集非常大（例如，大于 1 TB）时，或者如果有许多不同的文件夹，则应考虑使用多个 DistCp 作业。 可能没有任何性能提升，但它会展开作业，因此如果有任何作业失败，只需重启该特定作业（而不是整个作业）。

### <a name="limitations"></a>限制

* DistCp 会尝试创建大小类似的映射器以优化性能。 增加映射器数不一定始终会提高性能。

* DistCp 被限制为每个文件只有一个映射器。 因此，映射器数不应超过拥有的文件数。 由于 DistCp 只能将一个映射器分配给一个文件，这会限制可用于复制大型文件的并发数量。

* 如果有少量大型文件，则应将它们拆分为 256 MB 的文件块，以便提供更多潜在并发。 
 
* 如果要从 Azure Blob 存储帐户复制，可能会在 blob 存储端限制复制作业。 这会降低复制作业的性能。 若要了解有关 Azure Blob 存储限制的详细信息，请在 [Azure 订阅和服务限制](../azure-subscription-service-limits.md)中查看 Azure 存储限制。

## <a name="see-also"></a>另请参阅
* [从 Azure 存储 blob 复制数据到 Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
