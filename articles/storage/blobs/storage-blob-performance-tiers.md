---
title: 阻止 Blob 存储性能层 = Azure 存储
description: 讨论 Azure 块 Blob 存储的高级性能和标准性能层之间的区别。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270228"
---
# <a name="performance-tiers-for-block-blob-storage"></a>块 Blob 存储的性能层

由于企业部署的是性能敏感的云本机应用程序，因此在不同的性能级别上提供经济高效的数据存储选项非常重要。

Azure 块 Blob 存储提供两个不同的性能层：

- **高级**： 针对高事务速率和单位一致存储延迟进行了优化
- **标准**：针对高容量和高吞吐量进行了优化

以下注意事项适用于不同的性能层：

| 区域 |标准性能  |卓越的性能  |
|---------|---------|---------|
|上市区域     |   所有区域      | 在[选定区域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|受支持的[存储帐户类型](../common/storage-account-overview.md#types-of-storage-accounts)     |     通用 v2， Blob 存储， 通用 v1    |    块 Blob 存储     |
|支持[高吞吐量块 Blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    是，在大于 4 MiB PutBlock 或 PutBlob 大小     |    是，在大于 256 KiB PutBlock 或 PutBlob 大小    |
|冗余     |     请参阅[存储帐户的类型](../common/storage-account-overview.md#types-of-storage-accounts)   |  目前仅支持本地冗余存储 （LRS） 和区域重新集中存储 （ZRS）<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup>区域冗余存储 （ZRS） 在高级性能块 Blob 存储帐户的选定区域中可用。</div>

在成本方面，高级性能为具有高事务速率的应用程序提供优化的定价，以帮助降低这些工作负载[的总存储成本](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/)。

## <a name="premium-performance"></a>卓越的性能

高性能块 Blob 存储使数据可通过高性能硬件提供。 数据存储在固态驱动器 （SSD） 上，这些驱动器针对低延迟进行了优化。 与传统硬盘相比，SSD 提供更高的吞吐量。

高级性能存储非常适合需要快速一致的响应时间的工作负载。 它最适合执行许多小事务的工作负载。 工作负荷示例包括：

- **交互式工作负载**。 这些工作负载需要即时更新和用户反馈，例如电子商务和映射应用程序。 例如，在电子商务应用程序中，不太频繁查看的项目可能不会缓存。 但是，必须按需向客户立即显示它们。

- **分析**. 在 IoT 方案中，可能会每秒将许多较小的写入操作推送到云中。 可能会获取大量数据，为分析目的进行聚合，然后几乎立即删除。 高级块 Blob 存储的高引入功能使其高效地用于此类工作负载。

- **人工智能/机器学习 （AI/ML）**. AI/ML 处理不同数据类型（如视觉对象、语音和文本）的消耗和处理。 这种高性能计算类型的工作负载处理大量数据，这些数据需要快速响应和高效的引入时间进行数据分析。

- **数据转换**。 需要持续编辑、修改和转换数据的过程需要即时更新。 为了准确进行数据表示，此数据的使用者必须立即看到这些更改的反映。

## <a name="standard-performance"></a>标准性能

标准性能支持不同的[访问层](storage-blob-storage-tiers.md)以最经济高效的方式存储数据。 它已针对大型数据集的高容量和高吞吐量进行了优化。

- **备份和灾难恢复数据集**。 标准性能存储提供经济高效的层级，使其成为短期和长期灾难恢复数据集、辅助备份和合规性数据存档的完美用例。

- **媒体内容**. 首次创建和存储时，通常经常访问图像和视频，但此内容类型在变老时使用的频率较低。 标准性能存储提供适合媒体内容需求的层。 

- **批量数据处理**。 这些类型的工作负载适用于标准存储，因为它们需要经济高效的高吞吐量存储，而不是一致的低延迟。 大型原始数据集将暂存处理，并最终迁移到较冷层。

## <a name="migrate-from-standard-to-premium"></a>从标准迁移到高级

不能将现有标准性能存储帐户转换为具有高级性能的块 Blob 存储帐户。 要迁移到高级性能存储帐户，必须创建一个 BlockBlob 存储帐户，并将数据迁移到新帐户。 有关详细信息，请参阅[创建块 Blob 存储帐户](storage-blob-create-account-block-blob.md)。

要在存储帐户之间复制 Blob，可以使用最新版本的[AzCopy](../common/storage-use-azcopy-blobs.md)命令行工具。 其他工具（如 Azure 数据工厂）也可用于数据移动和转换。

## <a name="blob-lifecycle-management"></a>Blob 生命周期管理

Blob 存储生命周期管理提供了丰富的、基于规则的策略：

- **高级**：在其生命周期结束时过期数据。
- **标准**：将数据转换为最佳访问层，并在其生命周期结束时过期数据。

若要了解详细信息，请参阅[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)。

不能在热、酷和存档层之间移动存储在高级块 Blob 存储帐户中的数据。 但是，您可以将 Blob 从块 Blob 存储帐户复制到*其他*帐户中的热访问层。 要将数据复制到其他帐户，请使用["从 URL API 放置块"](/rest/api/storageservices/put-block-from-url)或["AzCopy v10](../common/storage-use-azcopy-v10.md)"。 **从 URL 的放置块**API 同步复制服务器上的数据。 只有在所有数据从原始服务器位置移动到目标位置后，呼叫才会完成。

## <a name="next-steps"></a>后续步骤

评估 GPv2 和 Blob 存储帐户中的热、酷和存档。

- [了解如何从存档层解冻 Blob 数据](storage-blob-rehydration.md)
- [通过启用 Azure 存储度量值来评估当前存储帐户的使用情况](../common/storage-enable-and-view-metrics.md)
- [按区域查看 Blob 存储和 GPv2 帐户中的热、酷和存档定价](https://azure.microsoft.com/pricing/details/storage/)
- [检查数据传输定价](https://azure.microsoft.com/pricing/details/data-transfers/)
