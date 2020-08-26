---
title: blob 的软删除
titleSuffix: Azure Storage
description: Blob 软删除可保护你的数据，以便在应用程序或其他存储帐户用户错误地修改或删除数据时，可以更轻松地恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2e390c9d5d2fa7c6551ed661c6c25096732eefd5
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057170"
---
# <a name="soft-delete-for-blobs"></a>blob 的软删除

Blob 的软删除可防止意外或错误地修改或删除数据。 为存储帐户启用 blob 的软删除时，该存储帐户中的 blob、blob 版本 (预览) 和快照可能会在你指定的保持期内恢复。

如果你的数据有可能被应用程序或其他存储帐户用户意外修改或删除，Microsoft 建议启用软删除。 有关启用软删除的详细信息，请参阅[为 Blob 启用和管理软删除](soft-delete-enable.md)。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete-for-blobs"></a>关于 blob 的软删除

在存储帐户上启用 blob 的软删除时，可以在指定的数据保持期内恢复已删除的对象。 此保护扩展到) 的任何 blob (块 blob、追加 blob 或页 blob，作为覆盖的结果被删除。

如果在启用了 blob 软删除的情况下删除了现有 blob 或快照中的数据，但未启用 blob 版本控制 (预览) ，则会生成软删除的快照以保存覆盖的数据的状态。 在指定的保持期到期后，将永久删除该对象。

如果在存储帐户上同时启用 blob 版本控制和 blob 软删除，则删除 blob 会创建新版本，而不是软删除快照。 不会软删除新版本，并且在软删除保留期到期时不会删除。 可以通过调用 "[撤消删除 blob](/rest/api/storageservices/undelete-blob) " 操作在保留期内还原 blob 的软删除版本。 然后，可以通过调用 "[复制 blob](/rest/api/storageservices/copy-blob) " 操作，从它的某个版本中还原 blob。 有关将 blob 版本控制和软删除一起使用的详细信息，请参阅[blob 版本控制和软删除](versioning-overview.md#blob-versioning-and-soft-delete)。

除非显式列出，否则软删除对象不可见。

Blob 软删除是向后兼容的，因此，无需对应用程序进行任何更改即可利用此功能提供的保护。 但是，[数据恢复](#recovery)会引入新的撤销删除 Blob API。

Blob 软删除适用于新的和现有的常规用途 v2、常规用途 v1 和 Blob 存储帐户。 支持标准和高级帐户类型。 Blob 软删除适用于所有存储层，包括热、冷和存档。 软删除适用于非托管磁盘（实际上是页 blob），但不可用于托管磁盘。

### <a name="configuration-settings"></a>配置设置

创建新帐户时，默认禁用软删除。 对于现有的存储帐户，默认情况下也禁用软删除。 可以在任何时候启用或禁用存储帐户的软删除。

启用软删除时，必须配置保持期。 保持期指示已软删除数据的存储和可恢复时间期限。 对于显式删除的对象，保持期从删除数据时开始计时。 对于覆盖数据时软删除功能生成的软删除版本或快照，保持期从生成版本或快照时开始计时。 保持期可能介于 1 到 365 天之间。

可随时更改软删除保持期。 更新后的保持期仅适用于新删除的数据。 之前删除的数据的过期时间取决于删除该数据时配置的保持期。 尝试删除某个软删除的对象不会影响其到期时间。

如果禁用软删除，则可以继续访问和恢复启用该功能时保存在存储帐户中的软删除数据。

### <a name="saving-deleted-data"></a>保存已删除的数据

软删除在删除或覆盖对象的许多情况下保存数据。

使用“放置 Blob”、“放置块列表”或“复制 Blob”覆盖 blob 时，将自动生成写入操作前 blob 的状态的版本或快照  。 除非显式列出，否则软删除对象不可见。 请参阅[恢复](#recovery)部分，了解如何列出软删除对象。

![此图显示了如何使用 Put Blob、Put 块列表或复制 Blob 覆盖 blob 的快照。](media/soft-delete-blob-overview/storage-blob-soft-delete-overwrite.png)

*软删除数据呈现为灰色，而活动数据为蓝色。新写入的数据显示在旧数据下方。使用 B1 覆盖 B0 时会生成 B0 的软删除快照。使用 B2 覆盖 B1 时会生成 B1 的软删除快照。

> [!NOTE]  
> 对目标 blob 的帐户启用软删除时，软删除仅对复制操作提供覆盖保护。

> [!NOTE]  
> 软删除不会对存档层中的 blob 提供覆盖保护。 如果存档层中的 blob 被任何层中的新 blob 覆盖，则被覆盖的 blob 将永久过期。

对快照调用“删除 Blob”时，该快照会被标记为软删除。 此时不会生成新的快照。

![显示使用 "删除 Blob" 时如何软删除 blob 快照的关系图。](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*软删除数据呈现为灰色，而活动数据为蓝色。新写入的数据显示在旧数据下方。调用**Snapshot Blob**时，B0 将变为快照，B1 成为该 blob 的活动状态。如果删除 B0 快照，它将被标记为软删除*。

如果对基础 blob（本身不是快照的任何 blob）调用“删除 Blob”，该 blob 将被标记为软删除。 与以前的行为一致，对具有活动快照的 blob 调用“删除 Blob”将返回错误。 对具有软删除快照的 blob 调用“删除 Blob”不会返回错误。 启用软删除后，仍可在单个操作中删除 blob 及其所有快照。 执行该操作会将基础 blob 和快照标记为软删除。

![此图显示了在基本 blob 上调用删除博客时会发生的情况。](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-include.png)

*软删除数据呈现为灰色，而活动数据为蓝色。新写入的数据显示在旧数据下方。此处调用了“删除 Blob”来删除 B2 和所有相关快照 。活动 blob B2 和所有相关快照均被标记为软删除。

> [!NOTE]  
> 覆盖软删除 blob 时，将自动生成写入操作前 blob 状态的软删除快照。 新 blob 将继承被覆盖 blob 的层级。

如果删除容器或帐户或者覆盖 blob 元数据和 blob 属性，软删除皆不会保存数据。 若要防止存储帐户被错误删除，可以使用 Azure 资源管理器配置锁定。 有关详细信息，请参阅 Azure 资源管理器文章[锁定资源以防止意外更改](../../azure-resource-manager/management/lock-resources.md)。

下表详述了启用软删除后的预期行为：

| REST API 操作 | 资源类型 | 说明 | 行为更改 |
|--------------------|---------------|-------------|--------------------|
| [删除](/rest/api/storagerp/StorageAccounts/Delete) | 帐户 | 删除存储帐户，包括它包含的所有容器和 blob。                           | 无更改。 已删除帐户中的容器和 blob 不可恢复。 |
| [删除容器](/rest/api/storageservices/delete-container) | 容器 | 删除容器，包括它包含的所有 blob。 | 无更改。 已删除容器中的 blob 不可恢复。 |
| [放置 Blob](/rest/api/storageservices/put-blob) | 块、追加和页 blob | 创建新的 blob 或替换容器内的现有 blob | 如果用于替换现有 blob，将自动生成调用之前的 blob 状态的快照。 对于以前软删除的 blob，当且仅当使用相同类型的 blob （块、追加或页 blob）进行替换时，才会生成快照。 如果由不同类型的 blob 替换，所有现有软删除数据都将永久过期。 |
| [删除 Blob](/rest/api/storageservices/delete-blob) | 块、追加和页 blob | 标记要删除的 blob 或 blob 快照。 blob 或快照将稍后在垃圾回收过程中进行删除 | 如果用于删除 blob 快照，该快照将标记为软删除。 如果用于删除 blob，该 blob 将标记为软删除。 |
| [复制 Blob](/rest/api/storageservices/copy-blob) | 块、追加和页 blob | 将源 blob 复制到相同存储帐户或其他存储帐户中的目标 blob 中。 | 如果用于替换现有 blob，将自动生成调用之前的 blob 状态的快照。 对于以前软删除的 blob，当且仅当使用相同类型的 blob （块、追加或页 blob）进行替换时，才会生成快照。 如果由不同类型的 blob 替换，所有现有软删除数据都将永久过期。 |
| [放置块](/rest/api/storageservices/put-block) | 块 Blob | 创建新块，作为块 blob 的一部分进行提交。 | 如果用于将块提交到活动 blob 中，则不发生任何更改。 如果用于将块提交到软删除的 blob 中，将创建新的 blob 并自动生成快照，以捕获软删除 blob 的状态。 |
| [放置块列表](/rest/api/storageservices/put-block-list) | 块 Blob | 通过指定构成块 blob 的块 ID 集来提交 blob。 | 如果用于替换现有 blob，将自动生成调用之前的 blob 状态的快照。 对于以前软删除的 blob，当且仅当其为块 blob 时，才会生成快照。 如果由不同类型的 blob 替换，所有现有软删除数据都将永久过期。 |
| [放置页](/rest/api/storageservices/put-page) | 页 Blob | 将一系列页写入页 blob。 | 无更改。 通过该操作覆盖或清除的页 blob 数据不会保存，且不可恢复。 |
| [追加块](/rest/api/storageservices/append-block) | 追加 Blob | 将数据块写入追加 blob 的末尾 | 无更改。 |
| [设置 Blob 属性](/rest/api/storageservices/set-blob-properties) | 块、追加和页 blob | 为对 blob 定义的系统属性设置值。 | 无更改。 被覆盖的 blob 属性不可恢复。 |
| [设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata) | 块、追加和页 blob | 将特定 blob 的用户定义元数据设置为一个或多个名称/值对。 | 无更改。 被覆盖的 blob 元数据不可恢复。 |

请务必注意，通过调用“Put Page”来覆盖或清除页 blob 的范围时不会自动生成快照。 虚拟机磁盘受页 blob 支持，且使用“放置页”来写入数据。

### <a name="recovery"></a>恢复

对软删除的基础 blob 调用[撤销删除 Blob](/rest/api/storageservices/undelete-blob) 操作会将该 blob 及所有相关软删除快照还原为活动状态。 对活动基础 blob 调用“撤销删除 Blob”操作会将所有相关软删除快照还原为活动状态。 快照还原为活动状态后与用户生成的快照相似；这些快照不会覆盖基础 blob。

若要将 blob 还原到特定的软删除快照，可对基础 blob 调用“撤销删除 Blob”。 然后可将该快照复制到现在处于活动状态的 blob。 也可将该快照复制到新的 blob 中。

![此图显示了在使用 "撤消删除 blob" 时会发生的情况。](media/soft-delete-blob-overview/storage-blob-soft-delete-recover.png)

*软删除数据呈现为灰色，而活动数据为蓝色。新写入的数据显示在旧数据下方。此处对 blob B 调用了**撤销删除 Blob**，从而将基础 blob B1 和所有相关快照（此处仅为 B0）还原为活动状态。第二步中将 B0 复制到了基础 blob。此复制操作将生成 B1 的软删除快照*。

若要查看软删除 blob 和 blob 快照，可选择将已删除数据包含在列表 Blob 中。 可选择仅查看软删除的基础 blob，或者也将软删除的 blob 快照包含在内。 对于所有软删除数据，可以查数据删除的时间以及数据永久过期的剩余天数。

### <a name="example"></a>示例

启用软删除后，对名为 *HelloWorld* 的 blob 执行上传、覆盖、拍摄快照、删除和还原操作。下面为其 .NET 脚本的控制台输出：

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

请参阅[后续步骤](#next-steps)部分，了解产生此输出的应用程序的指针。

## <a name="pricing-and-billing"></a>定价和计费

所有软删除数据按与活动数据相同的费率计费。 不会对在配置的保持期后永久删除的数据计费。 若要深入了解快照以及它们如何产生费用，请参阅[了解快照如何产生费用](storage-blob-snapshots.md)。

不会对快照自动生成相关事务进行计费。 将按写入操作费率对“撤销删除 Blob ”事务进行计费。

有关 Azure Blob 存储常规价格的更多详细信息，请参阅 [Azure Blob 存储定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。

初次启用软删除时，Microsoft 建议使用较短的保留期来更好地了解该功能将如何影响帐单。

为频繁覆盖的数据启用软删除可能会导致在列出 Blob 时存储容量费用增加且延迟增加。 可以通过将频繁覆盖的数据存储在禁用了软删除的单独存储帐户中来缓解这种额外的成本和延迟问题。

## <a name="faq"></a>常见问题

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>是否可以使用“设置 Blob 层 API”将 Blob 与软删除的快照置于一层？

是的。 软删除的快照会保留在原始层中，但基础 Blob 会移到新层中。

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>高级存储帐户每个 Blob 的快照上限为 100。 软删除快照是否计入此限制？

不，软删除快照不记入此限制。

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>如果在启用软删除的情况下删除整个帐户或容器，是否会保存所有相关 Blob？

不会，如果删除整个帐户或容器，将永久删除所有相关 blob。 若要了详细解如何防止意外删除存储帐户，请参阅[锁定资源以防止意外更改](../../azure-resource-manager/management/lock-resources.md)。

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>能否查看已删除数据的容量指标？

软删除数据属于存储帐户总容量的一部分。 有关跟踪和监视存储容量的详细信息，请参阅[存储分析](../common/storage-analytics.md)。

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>能否读取和复制 Blob 的软删除快照？  

可以，但必须首先对该 blob 调用撤销删除。

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>软删除是否适用于虚拟机磁盘？  

软删除适用于高级和标准非托管磁盘（实际上是页 blob）。 软删除只能帮助恢复由“删除 Blob”、“放置 Blob”、“放置块列表”和“复制 Blob”操作删除的数据   。 通过调用放置页覆盖的数据不可恢复。

Azure 虚拟机通过调用“放置页”来写入非托管磁盘，因此不支持使用软删除来撤消从 Azure VM 写入到非托管磁盘的操作。

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>是否需要更改现有应用程序才能使用软删除？

无论使用何种 API 版本，均可以使用软删除。 但是，若要列出和恢复软删除 blob 和 blob 快照，则需要使用 2017-07-29 版 [Azure 存储 REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 或更高版本。 Microsoft 建议始终使用最新版本的 Azure 存储 API。

## <a name="next-steps"></a>后续步骤

- [为 blob 启用软删除](soft-delete-enable.md)
- [Blob 版本控制（预览版）](versioning-overview.md)
