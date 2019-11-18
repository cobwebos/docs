---
title: 从存档层解冻 Blob 数据
description: 从存档存储中解冻 Blob，以便可以访问数据。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: d6370509b49ae464b53525e7320676b04912bd12
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113709"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>从存档层解冻 Blob 数据

当 Blob 位于存档访问层时，它被视为脱机状态，无法对其进行读取或修改。 Blob 元数据保持联机和可用状态，你可以列出 Blob 及其属性。 只能读取和修改联机层（例如热层或冷层）中的 Blob 数据。 可使用两个选项来检索和访问存档访问层中存储的数据。

1. 使用 "[设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)" 操作将[存档的 blob 解除冻结到联机层](#rehydrate-an-archived-blob-to-an-online-tier)，将存档 blob 解除冻结为热或冷。
2. [将已存档的 Blob 复制到联机层](#copy-an-archived-blob-to-an-online-tier)-通过使用 "[复制 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) " 操作创建存档 blob 的新副本。 指定不同的 Blob 名称，以及目标热层或冷层。

 有关层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](storage-blob-storage-tiers.md)。

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>将存档的 Blob 解冻到联机层

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>将存档的 Blob 复制到联机层

如果不想解除冻结存档 blob，可以选择执行[复制 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作。 原始 blob 在存档中仍将保持不变，而在联机热层或冷层中创建新的 blob 以便于使用。 在 "复制 Blob" 操作中，你还可以将可选的 "*解除冻结-优先级*" 属性设置为 "标准" 或 "高" （预览）以指定你希望创建 Blob 副本的优先级。

只能将存档 blob 复制到同一存储帐户中的联机目标层。 不支持将存档 blob 复制到另一个存档 blob。

根据所选的解除冻结优先级，从存档复制 blob 可能需要数小时才能完成。 在后台，"**复制 Blob** " 操作将读取存档源 Blob，以便在所选目标层中创建新的联机 Blob。 当你列出 blob 时，新的 blob 可能会可见，但在源存档 blob 完成读取并将数据写入新的联机目标 blob 之前，数据将不可用。 新 blob 作为独立副本，对其进行的任何修改或删除都不会影响源存档 blob。

## <a name="pricing-and-billing"></a>定价和计费

将存档中的 Blob 解冻到热层或冷层会产生读取操作和数据检索费用。 与使用标准优先级相比，使用高优先级（预览版功能）产生的操作和数据检索费用更高。 高优先级解除冻结在帐单上显示为单独的行项。 如果高优先级请求返回几个千兆字节的存档 blob 需要超过5小时，则不会向你收取高优先级检索速率。 但是，标准检索速率仍适用，因为解除冻结的优先级高于其他请求。

将存档中的 Blob 复制到热层或冷层会产生读取操作和数据检索费用。 创建新的 blob 副本需要支付写入操作的费用。 复制到联机 Blob 时，不会产生提前删除费，因为源 Blob 在存档层中保持未修改状态。 如果选中，将应用高优先级检索费用。

存档层中的 Blob 应至少存储 180 天。 在 180 天之前删除或解冻存档的 Blob 会导致提前删除费。

> [!NOTE]
> 有关块 Blob 和数据解冻的定价详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。 有关出站数据传输费的详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="next-steps"></a>后续步骤

* [了解 Blob 存储层](storage-blob-storage-tiers.md)
* [按区域查看 Blob 存储帐户和 GPv2 帐户中的热层、冷层和存档层定价](https://azure.microsoft.com/pricing/details/storage/)
* [管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)
* [检查数据传输定价](https://azure.microsoft.com/pricing/details/data-transfers/)
