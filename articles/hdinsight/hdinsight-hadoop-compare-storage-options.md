---
title: 比较用于与 Azure HDInsight 群集配合使用的存储选项
description: 概述存储类型及其如何使用 Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: a866a225da87c22a3a276a5d59b8e86f1f955cae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856188"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>比较用于与 Azure HDInsight 群集配合使用的存储选项

创建 HDInsight 群集时，可以在几个不同的 Azure 存储服务之间进行选择：

* [Azure Blob 存储与 HDInsight](./overview-azure-storage.md)
* [与 HDInsight Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [与 HDInsight Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)

本文概述了这些存储类型和其独特功能。

## <a name="storage-types-and-features"></a>存储类型和功能

下表汇总了不同版本的 HDInsight 支持的 Azure 存储服务：

| 存储服务 | 帐户类型 | 命名空间类型 | 支持的服务 | 支持的性能层 | 支持的访问层 | HDInsight 版本 | 群集类型 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 常规用途 V2 | 分层 (filesystem)  | Blob | 标准 | 热、冷、存档 | 3.6+ | 除 Spark 2.1 和2.2 之外的所有|
|Azure 存储| 常规用途 V2 | 对象 | Blob | 标准 | 热、冷、存档 | 3.6+ | 全部 |
|Azure 存储| 常规用途 V1 | 对象 | Blob | Standard | 空值 | All | 全部 |
|Azure 存储| Blob 存储 * * | 对象 | 块 blob | 标准 | 热、冷、存档 | 全部 | 全部 |
|Azure Data Lake Storage Gen1| 空值 | 分层 (filesystem)  | 空值 | 空值 | 空值 | 仅3。6 | 除 HBase 之外的所有 |

* * 对于 HDInsight 群集，只有辅助存储帐户的类型为 BlobStorage，页 Blob 不是受支持的存储选项。

有关 Azure 存储帐户类型的详细信息，请参阅 [azure 存储帐户概述](../storage/common/storage-account-overview.md)

有关 Azure 存储访问层的详细信息，请参阅 [Azure Blob 存储：高级 (预览) 、热、冷和存档存储层](../storage/blobs/storage-blob-storage-tiers.md)

可以使用服务组合创建群集，以用于主要和可选的辅助存储。 下表总结了 HDInsight 当前支持的群集存储配置：

| HDInsight 版本 | 主存储 | 辅助存储 | 支持 |
|---|---|---|---|
| 3.6 & 4。0 | 常规用途 V1，常规用途 V2 | 常规用途 V1，常规用途 V2，BlobStorage (块 Blob)  | “是” |
| 3.6 & 4。0 | 常规用途 V1，常规用途 V2 | Data Lake Storage Gen2 | 否 |
| 3.6 & 4。0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | “是” |
| 3.6 & 4。0 | Data Lake Storage Gen2 * | 常规用途 V1，常规用途 V2，BlobStorage (块 Blob)  | “是” |
| 3.6 & 4。0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 否 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | “是” |
| 3.6 | Data Lake Storage Gen1 | 常规用途 V1，常规用途 V2，BlobStorage (块 Blob)  | “是” |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 否 |
| 4.0 | Data Lake Storage Gen1 | Any | 否 |
| 4.0 | 常规用途 V1，常规用途 V2 | Data Lake Storage Gen1 | 否 |

* = 这可能是一个或多个 Data Lake Storage Gen2，只要它们都设置为使用相同的托管标识进行群集访问。

> [!NOTE]
> Spark 2.1 或2.2 群集不支持主存储 Data Lake Storage Gen2。

## <a name="data-replication"></a>数据复制

Azure HDInsight 不存储客户数据。 群集存储的主要方法是其关联的存储帐户。 可以将群集附加到现有存储帐户，或在群集创建过程中创建新的存储帐户。 如果创建了新帐户，则会将其创建为本地冗余存储 (LRS) 帐户，并满足区域内数据派驻要求，其中包括在 [信任中心](https://azuredatacentermap.azurewebsites.net)中指定的帐户。

可以通过确保与 HDInsight 关联的存储帐户为 LRS 或在 [信任中心](https://azuredatacentermap.azurewebsites.net)提及的其他存储选项，验证 hdinsight 是否已正确配置为将数据存储在单个区域中。
 
## <a name="next-steps"></a>后续步骤

* [HDInsight 中的 Azure 存储概述](./overview-azure-storage.md)
* [HDInsight 中的 Azure Data Lake Storage Gen1 概述](./overview-data-lake-storage-gen1.md)
* [HDInsight 中的 Azure Data Lake Storage Gen2 概述](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 存储简介](../storage/common/storage-introduction.md)
