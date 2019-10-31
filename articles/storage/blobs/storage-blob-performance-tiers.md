---
title: Azure 块 Blob 存储性能层-Azure 存储空间
description: Azure blob 存储的性能层。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: e0d746f1b01784bc383c12543936f06dae66ca09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063256"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Azure 块 Blob 存储性能层

企业部署性能敏感的云本机应用程序时，必须在不同的性能级别上提供经济高效的数据存储选项。

Azure 块 blob 存储提供两个不同的性能层：

- 高级：针对高事务速率和单位数一致性存储延迟进行了优化
- 标准：针对高容量和高吞吐量进行优化

以下注意事项适用于不同的性能层：

- 标准性能适用于所有[Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。 高级性能在[选择区域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)中可用。
- 高级性能为具有高事务速率的应用程序提供了优化定价，有助于降低这些工作负荷的[总存储成本](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/)。
- 若要获取块 blob 的高级性能，必须使用 BlockBlobStorage 帐户类型。
- 标准性能适用于常规用途 v1、常规用途 v2 和 Blob 存储帐户。
- 高级和标准性能都支持[高吞吐量块 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)。 高吞吐量块 blob 适用于 256 KiB 以上的高级性能。 高吞吐量块 blob 可用于超过 4 MiB Put 块或 Put Blob 大小的标准性能。
- 高级性能目前仅适用于本地冗余存储（LRS）。

## <a name="premium-performance"></a>高级性能

高级性能块 blob 存储通过高性能硬件使数据可用。 数据存储在固态硬盘（Ssd）上，已针对低延迟进行了优化。 与传统硬盘相比，Ssd 提供更高的吞吐量。

高级性能块 blob 存储非常适合于需要快速而一致的响应时间的工作负荷。 它最适用于执行许多小型事务的工作负荷。

## <a name="standard-performance"></a>标准性能

标准性能支持不同的[访问层](storage-blob-storage-tiers.md)以最经济高效的方式存储数据。 它针对大型数据集的高容量和高吞吐量进行了优化。

## <a name="blob-lifecycle-management"></a>Blob 生命周期管理

Blob 存储生命周期管理提供了基于规则的丰富策略：

- "高级"-在其生命周期结束时过期数据
- 标准-将数据转换为最佳访问层，并使数据在其生命周期结束时过期

若要了解详细信息，请参阅[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)。

无法在 "热"、"冷" 和 "存档" 层之间移动高级块 blob 存储帐户中存储的数据。 但是，可以将 blob 从块 blob 存储帐户复制到*其他*帐户中的热访问层。 使用[URL API 中的 Put 块](/rest/api/storageservices/put-block-from-url)或[AzCopy v10](../common/storage-use-azcopy-v10.md)将数据复制到其他帐户。 **来自 URL API 的 Put 块**同步复制服务器上的数据。 仅在将所有数据从原始服务器位置移至目标位置后，调用才会完成。

## <a name="next-steps"></a>后续步骤

评估 GPv2 和 Blob 存储帐户的热、冷和存档

- [按区域查看热层、冷层和存档层](https://azure.microsoft.com/regions/#services)
- [管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)
- [了解存档层中的解除冻结 blob 数据](storage-blob-rehydration.md)
- [通过启用 Azure 存储度量值来评估当前存储帐户的使用情况](../common/storage-enable-and-view-metrics.md)
- [按区域查看 Blob 存储帐户和 GPv2 帐户中的热层、冷层和存档层定价](https://azure.microsoft.com/pricing/details/storage/)
- [检查数据传输定价](https://azure.microsoft.com/pricing/details/data-transfers/)
