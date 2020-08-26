---
title: 块 Blob 存储性能层 — Azure 存储
description: 介绍 Azure 块 Blob 存储的高级与标准性能层之间的差异。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "74270228"
---
# <a name="performance-tiers-for-block-blob-storage"></a>块 Blob 存储的性能层

由于企业部署的是性能敏感的云本机应用程序，因此在不同的性能级别上提供经济高效的数据存储选项非常重要。

Azure 块 Blob 存储提供两个不同的性能层：

- **高级**：已针对高事务率和 10 毫秒以下的稳定存储延迟进行优化
- **标准**：已针对高容量和高吞吐量进行优化

以下注意事项适用于不同的性能层：

| 区域 |标准性能  |高级性能层  |
|---------|---------|---------|
|上市区域     |   所有区域      | [特定区域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|支持的[存储帐户类型](../common/storage-account-overview.md#types-of-storage-accounts)     |     常规用途 v2、Blob 存储、常规用途 v1    |    BlockBlobStorage     |
|支持[高吞吐量块 Blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    是，在大于 4 MiB PutBlock 或 PutBlob 大小时     |    是，在大于 256 KiB PutBlock 或 PutBlob 大小时    |
|冗余     |     请参阅[存储帐户的类型](../common/storage-account-overview.md#types-of-storage-accounts)   |  目前仅支持本地冗余存储 (LRS) 和区域冗余存储 (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup>可对特定区域中的高级性能层块 Blob 存储帐户使用区域冗余存储 (ZRS)。</div>

在成本方面，高级性能层为具有高事务率的应用程序提供优化的定价，以帮助[降低这些工作负荷的总存储成本](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/)。

## <a name="premium-performance"></a>高级性能层

高级性能层块 Blob 存储通过高性能硬件来提供数据。 数据存储在已针对低延迟进行优化的固态硬盘 (SSD) 上。 与传统的机械硬盘相比，SSD 提供更高的吞吐量。

对于需要快速且一致的响应时间的工作负荷而言，高级性能存储是理想之选。 它最适合用于执行许多小型事务的工作负荷。 示例工作负荷包括：

- **交互式工作负荷**。 这些工作负荷需要即时更新和用户反馈，例如电子商务和地图应用程序。 例如，在电子商务应用程序中，不经常查看的商品可能不会缓存。 但是，这些商品必须可按需立即向客户显示。

- **分析**。 在 IoT 方案中，每秒可能就会将大量的小规模写入操作推送到云中。 可能会提取大量的数据，将其聚合进行分析，然后立即将其删除。 高级块 Blob 存储的大规模引入功能使之能够为此类工作负荷带来效益。

- **人工智能/机器学习 (AI/ML)** 。 AI/ML 可以解决不同数据类型（例如视觉对象、语音和文本）的使用和处理。 这种高性能计算类型的工作负荷可以处理大量的、需要快速做出响应并在短时间内引入以进行高效数据分析的数据。

- **数据转换**。 需要不断编辑、修改和转换数据的过程需要即时更新。 为了准确表示数据，此数据的使用者必须立即看到这些更改。

## <a name="standard-performance"></a>标准性能

标准性能支持不同的[访问层](storage-blob-storage-tiers.md)以最经济高效的方式存储数据。 它已针对大型数据集的高容量和高吞吐量进行了优化。

- **备份和灾难恢复数据集**。 标准性能存储提供经济高效的层，使其成为短期和长期灾难恢复数据集、辅助备份与合规性数据存档的完美用例。

- **媒体内容**。 首次创建并存储图像和视频后，往往会频率地对其进行访问，但一段时间后，对此类内容的使用会越来越少。 标准性能存储提供适当的层来满足媒体内容需求。 

- **批量数据处理**。 此类工作负荷适合使用标准存储，因为它们需要经济高效的高吞吐量存储，而不是一致的低延迟。 较大的原始数据集将会暂存以进行处理，最终会迁移到较冷的层。

## <a name="migrate-from-standard-to-premium"></a>从标准层迁移到高级层

无法将现有的标准性能存储帐户转换为使用高级性能层的块 Blob 存储帐户。 若要迁移到高级性能存储帐户，必须创建一个块 Blob 存储帐户，并将数据迁移到新帐户。 有关详细信息，请参阅[创建块 Blob 存储帐户](storage-blob-create-account-block-blob.md)。

若要在存储帐户之间复制 Blob，可以使用最新版本的 [AzCopy](../common/storage-use-azcopy-blobs.md) 命令行工具。 Azure 数据工厂等其他工具也可用于数据移动和转换。

## <a name="blob-lifecycle-management"></a>Blob 生命周期管理

Blob 存储生命周期管理提供了丰富的基于规则的策略：

- **Premium**：在数据生命周期结束时使数据过期。
- **标准**：将数据转换到最佳访问层，并在其生命周期结束时使数据过期。

若要了解详细信息，请参阅[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)。

无法在热、冷和存档层之间移动存储在高级块 Blob 存储帐户中的数据。 但是，可将块 Blob 存储帐户中的 Blob 复制到不同帐户中的热访问层。  若要将数据复制到另一帐户，请使用[从 URL 放置块](/rest/api/storageservices/put-block-from-url) API 或 [AzCopy v10](../common/storage-use-azcopy-v10.md)。 “从 URL 放置块”API 以同步方式复制服务器上的数据。  仅在已将所有数据从原始服务器位置移到目标位置后，调用才会完成。

## <a name="next-steps"></a>后续步骤

评估 GPv2 和 Blob 存储帐户中的热、冷和存档层。

- [了解如何从存档层解冻 Blob 数据](storage-blob-rehydration.md)
- [通过启用 Azure 存储度量值来评估当前存储帐户的使用情况](../common/storage-enable-and-view-metrics.md)
- [按区域查看 Blob 存储帐户和 GPv2 帐户中的热层、冷层和存档层定价](https://azure.microsoft.com/pricing/details/storage/)
- [检查数据传输定价](https://azure.microsoft.com/pricing/details/data-transfers/)
