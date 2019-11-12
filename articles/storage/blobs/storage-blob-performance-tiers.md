---
title: 块 blob 存储性能层-Azure 存储
description: 讨论 Azure 块 blob 存储的高级和标准性能层之间的差异。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 64efd1a02b903ec3874066f6c663b86a8080f746
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932274"
---
# <a name="performance-tiers-for-block-blob-storage"></a>块 blob 存储的性能层

由于企业部署的是性能敏感的云本机应用程序，因此在不同的性能级别上提供经济高效的数据存储选项非常重要。

Azure 块 blob 存储提供两个不同的性能层：

- **高级**：针对高事务速率和单位数一致性存储延迟进行了优化
- **标准**：针对高容量和高吞吐量进行优化

以下注意事项适用于不同的性能层：

| 区域 |标准性能  |高级性能  |
|---------|---------|---------|
|上市区域     |   所有区域      | 在[选择区域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|支持的[存储帐户类型](../common/storage-account-overview.md#types-of-storage-accounts)     |     常规用途 v2，BlobStorage，常规用途 v1    |    BlockBlobStorage     |
|支持[高吞吐量块 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    是，大于 4 MiB PutBlock 或 PutBlob 大小     |    是，大于 256 KiB PutBlock 或 PutBlob 大小    |
|冗余     |     请参阅[存储帐户的类型](../common/storage-account-overview.md#types-of-storage-accounts)   |  目前仅支持本地冗余存储（LRS）和区域 redudant 存储（ZRS）<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup>区域冗余存储（ZRS）在高级性能块 blob 存储帐户的 "选择区域" 中提供。</div>

与成本相比，高级性能为具有高事务速率的应用程序提供了优化定价，有助于降低这些工作负荷的[总存储成本](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/)。

## <a name="premium-performance"></a>高级性能

高级性能块 blob 存储通过高性能硬件使数据可用。 数据存储在固态硬盘（Ssd）上，已针对低延迟进行了优化。 与传统硬盘相比，Ssd 提供更高的吞吐量。

高级性能存储非常适合于需要快速而一致的响应时间的工作负荷。 它最适用于执行许多小型事务的工作负荷。 示例工作负荷包括：

- **交互式工作负荷**。 这些工作负荷需要即时更新和用户反馈，如电子商务和映射应用程序。 例如，在电子商务应用程序中，可能不会缓存经常查看的项目。 但是，它们必须立即按需向客户显示。

- **分析**。 在 IoT 方案中，许多较小的写入操作可能会每秒推送到云。 可能会在中提取大量数据，并将其聚合起来，然后立即删除。 高级块 blob 存储的高引入功能使得这种类型的工作负荷更有效。

- **人工智能/机器学习（AI/ML）** 。 AI/ML 处理各种数据类型（如视觉对象、语音和文本）的消耗和处理。 这种高性能计算类型的工作负荷处理大量数据，这些数据需要快速响应并有效引入时间进行数据分析。

- **数据转换**。 需要对数据进行常量编辑、修改和转换的过程需要即时更新。 为实现准确的数据表示，此数据的使用者必须立即看到这些更改。

## <a name="standard-performance"></a>标准性能

标准性能支持不同的[访问层](storage-blob-storage-tiers.md)以最经济高效的方式存储数据。 它已针对大型数据集的高容量和高吞吐量进行了优化。

## <a name="migrate-from-standard-to-premium"></a>从标准迁移到高级

不能将现有的标准性能存储帐户转换为具有高级性能的块 blob 存储帐户。 若要迁移到高级性能存储帐户，必须创建 BlockBlobStorage 帐户，并将数据迁移到新帐户。 有关详细信息，请参阅[创建 BlockBlobStorage 帐户](storage-blob-create-account-block-blob.md)。

若要在存储帐户之间复制 blob，可以使用最新版本的[AzCopy](../common/storage-use-azcopy-blobs.md)命令行工具。 Azure 数据工厂等其他工具也可用于数据移动和转换。

## <a name="blob-lifecycle-management"></a>Blob 生命周期管理

Blob 存储生命周期管理提供了基于规则的丰富策略：

- **高级**：使数据在其生命周期结束时过期。
- **标准**：将数据转换为最佳访问层，并使数据在其生命周期结束时过期。

若要了解详细信息，请参阅[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)。

无法在 "热"、"冷" 和 "存档" 层之间移动存储在高级块 blob 存储帐户中的数据。 但是，可以将 blob 从块 blob 存储帐户复制到*其他*帐户中的热访问层。 若要将数据复制到其他帐户，请使用[URL API 中的 Put 块](/rest/api/storageservices/put-block-from-url)或[AzCopy v10](../common/storage-use-azcopy-v10.md)。 **来自 URL API 的 Put 块**同步复制服务器上的数据。 仅在将所有数据从原始服务器位置移至目标位置后，调用才会完成。

## <a name="next-steps"></a>后续步骤

评估 GPv2 和 Blob 存储帐户的热、冷和存档。

- [了解如何从存档层解冻 Blob 数据](storage-blob-rehydration.md)
- [通过启用 Azure 存储度量值来评估当前存储帐户的使用情况](../common/storage-enable-and-view-metrics.md)
- [按区域查看 Blob 存储帐户和 GPv2 帐户中的热层、冷层和存档层定价](https://azure.microsoft.com/pricing/details/storage/)
- [检查数据传输定价](https://azure.microsoft.com/pricing/details/data-transfers/)
