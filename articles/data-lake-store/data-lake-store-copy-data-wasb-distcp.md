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
ms.date: 10/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: dcab09385c6a664186d124ce3e042cc1fac82bd6


---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>使用 Distcp 在 Azure 存储 Blob 和 Data Lake Store 之间复制数据
> [!div class="op_single_selector"]
> * [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

创建具有 Data Lake Store 帐户访问权限的 HDInsight 群集后，可使用 Distcp 等 Hadoop 生态系统工具将数据复制到 HDInsight 群集存储 (WASB) 和从 HDInsight 群集存储 (WASB) 复制到 Data Lake Store 帐户。 本文提供如何实现此操作的说明。

## <a name="prerequisites"></a>先决条件
在开始阅读本文前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **启用 Azure 订阅** 进行 Data Lake Store 公共预览。 请参阅[说明](data-lake-store-get-started-portal.md)。
* 具有 Data Lake Store 帐户访问权限的**Azure HDInsight 群集**。 请参阅[创建包含 Data Lake Store 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。

## <a name="do-you-learn-fast-with-videos"></a>通过视频学得更快？
[观看此视频](https://mix.office.com/watch/1liuojvdx6sie)，了解如何使用 DistCp 在 Azure 存储 Blob 和 Data Lake Store 之间复制数据。

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>从远程桌面（Windows 群集）或 SSH （Linux 群集）使用 Distcp
HDInsight 群集附带 Distcp 实用工具，该实用工具可用于从不同源中复制数据到 HDInsight 群集。 如果已配置 HDInsight 将 Data Lake Store 用作其他存储，则 Distcp 实用工具也可独立用于向/从 Data Lake Store 帐户复制数据。 本部分介绍如何使用 Distcp 实用工具。

1. 如果具有 Windows 群集，请远程连接到具有 Data Lake Store 帐户访问权限的 HDInsight 群集。 有关说明，请参阅[使用 RDP 连接到群集](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)。 从群集桌面上打开 Hadoop 命令行。

    如果具有 Linux 群居，请使用 SSH 连接到该群集。 请参阅[连接到基于 Linux 的 HDInsight 群集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)。 通过 SSH 提示符运行命令。
2. 验证是否可访问 Azure 存储 Blob (WASB)。 运行以下命令：

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    这会提供此存储 blob 中内容的列表。
3. 同样，验证是否可从此群集访问 Data Lake Store 帐户。 运行以下命令：

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    这会提供 Data Lake Store 帐户中文件/文件夹的列表。
4. 使用 Distcp 从 WASB 复制数据到 Data Lake Store 帐户。

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    这会将 WASB 中 **/example/data/gutenberg/** 文件夹的内容复制到 Data Lake Store 帐户中的 **/myfolder**。
5. 同样，使用 Distcp 从 Data Lake Store 帐户复制数据到 WASB。

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    这会将 Data Lake Store 帐户中 **/myfolder** 的内容复制到 WASB 中的 **/example/data/gutenberg/** 文件夹。

## <a name="see-also"></a>另请参阅
* [从 Azure 存储 blob 复制数据到 Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Nov16_HO3-->


