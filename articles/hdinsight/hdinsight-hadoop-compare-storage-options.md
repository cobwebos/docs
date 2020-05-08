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
ms.openlocfilehash: ed93ba937a843618f36bac6e88b15ff77355ca75
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610694"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>比较用于与 Azure HDInsight 群集配合使用的存储选项

创建 HDInsight 群集时，可以在几个不同的 Azure 存储服务之间进行选择：

* [Azure 存储](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)

本文概述了这些存储类型和其独特功能。

## <a name="storage-types-and-features"></a>存储类型和功能

下表汇总了不同版本的 HDInsight 支持的 Azure 存储服务：

| 存储服务 | 帐户类型 | 命名空间类型 | 支持的服务 | 支持的性能层 | 支持的访问层 | HDInsight 版本 | 群集类型 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 常规用途 V2 | 层次结构（文件系统） | Blob | Standard | 热、冷、存档 | 3.6+ | 除 Spark 2.1 和2.2 之外的所有|
|Azure 存储| 常规用途 V2 | 对象 | Blob | Standard | 热、冷、存档 | 3.6+ | All |
|Azure 存储| 常规用途 V1 | 对象 | Blob | Standard | 不可用 | All | All |
|Azure 存储| Blob 存储 * * | 对象 | 块 blob | Standard | 热、冷、存档 | All | All |
|Azure Data Lake Storage Gen1| 不可用 | 层次结构（文件系统） | 不可用 | 不可用 | 不可用 | 仅3。6 | 除 HBase 之外的所有 |

* * 对于 HDInsight 群集，只有辅助存储帐户的类型为 BlobStorage，页 Blob 不是受支持的存储选项。

有关 Azure 存储帐户类型的详细信息，请参阅[azure 存储帐户概述](../storage/common/storage-account-overview.md)

有关 Azure 存储访问层的详细信息，请参阅[Azure Blob 存储：高级（预览版）、热、冷和存档存储层](../storage/blobs/storage-blob-storage-tiers.md)

可以使用服务组合创建群集，以用于主要和可选的辅助存储。 下表总结了 HDInsight 当前支持的群集存储配置：

| HDInsight 版本 | 主存储 | 辅助存储 | 支持 |
|---|---|---|---|
| 3.6 & 4。0 | 常规用途 V1，常规用途 V2 | 常规用途 V1，常规用途 V2，BlobStorage （块 Blob） | 是 |
| 3.6 & 4。0 | 常规用途 V1，常规用途 V2 | Data Lake Storage Gen2 | 否 |
| 3.6 & 4。0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | 是 |
| 3.6 & 4。0 | Data Lake Storage Gen2 * | 常规用途 V1，常规用途 V2，BlobStorage （块 Blob） | 是 |
| 3.6 & 4。0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 否 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 是 |
| 3.6 | Data Lake Storage Gen1 | 常规用途 V1，常规用途 V2，BlobStorage （块 Blob） | 是 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 否 |
| 4.0 | Data Lake Storage Gen1 | 任意 | 否 |
| 4.0 | 常规用途 V1，常规用途 V2 | Data Lake Storage Gen1 | 否 |

* = 这可能是一个或多个 Data Lake Storage Gen2 帐户，前提是它们都设置为使用相同的托管标识进行群集访问。

> [!NOTE]
> Spark 2.1 或2.2 群集不支持主存储 Data Lake Storage Gen2。

## <a name="next-steps"></a>后续步骤

* [Azure 存储概述](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen1 概述](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen2 概述](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 存储简介](../storage/common/storage-introduction.md)
