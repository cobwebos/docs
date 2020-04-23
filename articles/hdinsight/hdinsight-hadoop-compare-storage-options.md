---
title: 比较用于与 Azure HDInsight 群集配合使用的存储选项
description: 概述存储类型及其如何使用 Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869846"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>比较用于与 Azure HDInsight 群集配合使用的存储选项

创建 HDInsight 群集时，可以在几个不同的 Azure 存储服务之间进行选择：

* [Azure 存储](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

本文概述了这些存储类型和其独特功能。

## <a name="storage-types-and-features"></a>存储类型和功能

下表总结了不同版本的 HDInsight 支持的 Azure 存储服务：

| 存储服务 | 帐户类型 | 命名空间类型 | 支持的服务 | 支持的性能层 | 支持的访问层 | HDInsight 版本 | 群集类型 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 常规用途 V2 | 分层（文件系统） | Blob | Standard | 热、冷、存档 | 3.6+ | 除火花 2.1 和 2.2 外，所有|
|Azure 存储| 常规用途 V2 | 对象 | Blob | Standard | 热、冷、存档 | 3.6+ | All |
|Azure 存储| 常规用途 V1 | 对象 | Blob | Standard | 空值 | All | All |
|Azure 存储| Blob 存储* | 对象 | 块 blob | Standard | 热、冷、存档 | All | All |
|Azure Data Lake Storage Gen1| 空值 | 分层（文件系统） | 空值 | 空值 | 空值 | 3.6 仅 | 除 HBase 外 |

*对于 HDInsight 群集，只有辅助存储帐户的类型可以是 Blob 存储，页面 Blob 不是受支持的存储选项。

有关 Azure 存储帐户类型的详细信息，请参阅[Azure 存储帐户概述](../storage/common/storage-account-overview.md)

有关 Azure 存储访问层的详细信息，请参阅[Azure Blob 存储：高级（预览）、热存储、酷和存档存储层](../storage/blobs/storage-blob-storage-tiers.md)

您可以使用主存储和可选辅助存储的服务组合创建群集。 下表总结了 HDInsight 中当前支持的群集存储配置：

| HDInsight 版本 | 主存储 | 辅助存储 | 支持 |
|---|---|---|---|
| 3.6 & 4.0 | 通用 V1，通用 V2 | 通用 V1、通用 V2、Blob 存储（块 Blob） | 是 |
| 3.6 & 4.0 | 通用 V1，通用 V2 | Data Lake Storage Gen2 | 否 |
| 3.6 & 4.0 | 数据存储第2代* | Data Lake Storage Gen2 | 是 |
| 3.6 & 4.0 | 数据存储第2代* | 通用 V1、通用 V2、Blob 存储（块 Blob） | 是 |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 否 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 是 |
| 3.6 | Data Lake Storage Gen1 | 通用 V1、通用 V2、Blob 存储（块 Blob） | 是 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 否 |
| 4.0 | Data Lake Storage Gen1 | Any | 否 |
| 4.0 | 通用 V1，通用 V2 | Data Lake Storage Gen1 | 否 |

*这可能是一个或多个数据存储 Gen2 帐户，只要它们都设置为使用相同的托管标识进行群集访问。

> [!NOTE]
> Spark 2.1 或 2.2 群集不支持数据存储湖存储 Gen2 主存储。

## <a name="next-steps"></a>后续步骤

* [Azure 存储概述](./overview-azure-storage.md)
* [Azure 数据存储第 1 代概述](./overview-data-lake-storage-gen1.md)
* [Azure 数据存储第 2 代概述](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 存储简介](../storage/common/storage-introduction.md)
