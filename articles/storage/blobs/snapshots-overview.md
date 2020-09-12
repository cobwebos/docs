---
title: Blob 快照
titleSuffix: Azure Storage
description: 了解 blob 快照的工作原理以及如何对其进行计费。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ab4c152f30ab96fe5e221a605a2339c773e32547
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295388"
---
# <a name="blob-snapshots"></a>Blob 快照

快照是在某一时间点拍摄的只读版本的 Blob。

> [!NOTE]
> Blob 版本控制提供了一种更高级的方法来维护以前版本的 blob。 有关详细信息，请参阅 [Blob 版本控制](versioning-overview.md)。

## <a name="about-blob-snapshots"></a>关于 Blob 快照

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Blob 的快照与其基本 Blob 相同，不过，Blob URI 的后面追加了一个 **DateTime** 值，用于指示快照的生成时间。 例如，如果页 Blob URI 为 `http://storagesample.core.blob.windows.net/mydrives/myvhd`，则快照 URI 将类似于 `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`。

> [!NOTE]
> 所有快照共享基本 Blob 的 URI。 基本 Blob 与快照之间的唯一区别体现在追加的 **DateTime** 值。

一个 Blob 可以有任意数目的快照。 快照会一直保留，直到被显式删除（独立或作为基本 blob 的 [删除 Blob](/rest/api/storageservices/delete-blob) 操作的一部分）。 可以枚举与基本 Blob 关联的快照，以跟踪当前快照。

创建 Blob 的快照时，会将该 Blob 的系统属性复制到具有相同值的快照。 基本 Blob 的元数据也会复制到快照，除非创建快照时为其指定了单独的元数据。 创建快照后，可以读取、复制或删除它，但无法修改它。

任何与基本 Blob 关联的租约都不会影响快照。 无法获取快照上的租约。

VHD 文件用于存储 VM 磁盘的当前信息和状态。 可以将磁盘从 VM 分离或者关闭 VM，并拍摄其 VHD 文件的快照。 可以在以后使用该快照文件检索该时间点的 VHD 文件并重新创建 VM。

## <a name="understand-how-snapshots-accrue-charges"></a>了解快照如何产生费用

### <a name="important-billing-considerations"></a>重要计费注意事项

以下列表包含创建快照时要考虑的要点。

- 不管唯一的块或页是在 Blob 还是快照中，存储帐户都会产生费用。 在更新快照所基于的 Blob 之前，你的帐户不会就与 Blob 关联的快照产生额外费用。 更新基本 Blob 后，它与其快照分离。 发生这种情况时，需要支付每个 Blob 或快照中唯一块或页的费用。
- 在替换块 Blob 中的某个块后，会将该块作为唯一块进行收费。 即使该块具有的块 ID 和数据与它在快照中所具有的 ID 和数据相同也是如此。 重新提交块后，它将偏离它在任何快照中的对应部分，并且要为数据支付费用。 对于使用相同数据更新的页 Blob 中的页面来说，情况也是如此。
- 通过调用覆盖 blob 的全部内容的方法更新块 blob 将替换 blob 中的所有块。 如果你有与该 Blob 关联的快照，则基本 Blob 和快照中的所有块现在将发生偏离，你需要为这两个 Blob 中的所有块支付费用。 即使基本 Blob 和快照中的数据保持相同也是如此。
- Azure Blob 服务无法确定这两个块是否包含相同的数据。 每个上传和提交的块均被视为唯一的快，即使它具有相同的数据和块 ID 也是如此。 由于唯一的块会产生费用，因此必须考虑到：在更新具有快照的 Blob 时，会产生额外的唯一块，因此也会产生额外的费用。

### <a name="minimize-costs-with-snapshot-management"></a>通过快照管理降低成本

我们建议仔细管理快照，避免额外费用。 遵循下述最佳做法可以将存储快照的费用降至最低：

- 除非应用程序设计需要保留与 Blob 关联的快照，否则请在更新 Blob 时删除并重新创建这些快照，即使你使用相同的数据进行更新也是如此。 通过删除并重新创建 Blob 的快照，可以确保 Blob 和快照不会发生偏离。
- 如果要保留 blob 的快照，请避免在更新 blob 时调用覆盖整个 blob 的方法。 应更新尽可能少的块数，以降低成本。

### <a name="snapshot-billing-scenarios"></a>快照计费方案

下列方案说明了块 Blob 及其快照如何产生费用。

## <a name="pricing-and-billing"></a>定价和计费

创建快照（它是 Blob 的只读副本）会导致帐户产生额外的数据存储费用。 在设计应用程序时，有必要了解在哪些情况下会产生这些费用，以便最大程度地减少费用。

Blob 快照（如 blob 版本）按与活动数据相同的费率进行计费。 如何对快照进行计费取决于是否已为基本 blob 或其任何快照 (或版本) 显式设置层。 有关 blob 层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](storage-blob-storage-tiers.md)。

如果未更改 blob 或快照层，则会对该 blob 的唯一数据块、其快照以及它可能具有的任何版本计费。 有关详细信息，请参阅 [在尚未显式设置 blob 层时进行计费](#billing-when-the-blob-tier-has-not-been-explicitly-set)。

如果更改了 blob 或快照层，则会对整个对象计费，而不考虑 blob 和快照是否最终在同一层中。 有关详细信息，请参阅 [在显式设置 blob 层时进行计费](#billing-when-the-blob-tier-has-been-explicitly-set)。

有关 blob 版本计费详细信息的详细信息，请参阅 [blob 版本控制](versioning-overview.md)。

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>未显式设置 blob 层时进行计费

如果没有为基本 blob 或其任何快照显式设置 blob 层，则会为 blob、其快照以及它可能具有的任何版本的唯一块或页面付费。 跨 blob 和其快照共享的数据只收费一次。 在更新 blob 时，基础 blob 中的数据与其分离其快照中存储的数据，并按块或页面对唯一数据收费。

在替换块 Blob 中的某个块后，会将该块作为唯一块进行收费。 即使该块具有的块 ID 和数据与它在快照中所具有的 ID 和数据相同也是如此。 再次提交块后，它会从快照中的对应项与其分离，并向你收取其数据的费用。 对于使用相同数据更新的页 Blob 中的页面来说，情况也是如此。

Blob 存储无法确定两个块是否包含相同的数据。 每个上传和提交的块均被视为唯一的快，即使它具有相同的数据和块 ID 也是如此。 由于唯一的块会产生费用，因此请务必记住，当 blob 具有快照或版本时，更新 blob 将导致附加的唯一块和额外费用。

当 blob 具有快照时，对块 blob 调用更新操作，以使它们更新尽可能少的块。 允许对块进行精细控制的写入操作是 [put](/rest/api/storageservices/put-block) 块和 [put 块列表](/rest/api/storageservices/put-block-list)。 另一方面， [Put Blob](/rest/api/storageservices/put-blob) 操作将替换 Blob 的全部内容，因此可能会导致额外的费用。

下面的方案演示了在未显式设置 blob 层时，如何为块 blob 及其快照支付费用。

#### <a name="scenario-1"></a>方案 1

在方案 1 中，基本 Blob 自创建快照后未进行更新，因此仅唯一块 1、2 和 3 会产生费用。

![图1：显示基本 blob 和快照中的唯一块的计费。](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>方案 2

在方案 2 中，已更新基本 Blob，但未更新快照。 已更新块 3，即使它包含相同的数据和 ID，它也与快照中的块 3 不同。 因此，帐户需要为四个块支付费用。

![图2：显示基本 blob 和快照中的唯一块的计费。](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>方案 3

在方案 3 中，已更新基本 Blob，但未更新快照。 块 3 已替换为基础 Blob 中的块 4，但快照仍反映块 3。 因此，帐户需要为四个块支付费用。

![图3：显示基本 blob 和快照中的唯一块的计费。](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>方案 4

在方案 4 中，已完全更新基本 Blob，并且其中不包含任何原始块。 因此，帐户需要为所有八个唯一块支付费用。

![图4：显示基本 blob 和快照中的唯一块的计费。](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> 避免调用覆盖整个 blob 的方法，而是更新各个块以降低成本。

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>在显式设置 blob 层时计费

如果已显式设置 blob 或快照 (或版本) 的 blob 层，则会为新层中的对象的完整内容长度收费，而不考虑它是否与原始层中的对象共享块。 你还将对原始层中最早版本的完整内容长度收费。 保留在原始层中的任何版本或快照都按其共享的唯一块收费，如 [未显式设置 blob 层时计费](#billing-when-the-blob-tier-has-not-been-explicitly-set)中所述。

#### <a name="moving-a-blob-to-a-new-tier"></a>将 blob 移到新层

下表描述了将 blob 或快照移动到新层时的计费行为。

| 当显式设置 blob 层时 .。。 | 然后，你需要支付 .。。 |
|-|-|
| 带有快照的基本 blob | 新层中的基本 blob 和原始层中最旧的快照，以及其他快照中的任何唯一块。<sup>1</sup> |
| 具有以前版本和快照的基本 blob | 新层中的基本 blob、原始层中的最早版本和原始层中的最早快照，以及其他版本或快照中的任何唯一块<sup>1</sup>。 |
| 快照 | 新层中的快照和原始层中的基本 blob 以及其他快照中的任何唯一块。<sup>1</sup> |

<sup>1</sup>如果其他以前的版本或快照尚未从其原始层中移出，这些版本或快照会根据它们所包含的唯一块的数量收费，如 [未显式设置 blob 层的计费](#billing-when-the-blob-tier-has-not-been-explicitly-set)中所述。

下图说明了当包含快照的 blob 移到不同层时，如何对对象计费。

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="此图显示了在显式分层包含快照的 blob 时，如何对对象计费。":::

无法撤消为 blob、版本或快照显式设置层。 如果将某个 blob 移动到新层，然后将其移回其原始层，则即使该对象与原始层中的其他对象共享块，也会为该对象的完整内容长度收费。

显式设置 blob、版本或快照层的操作包括：

- [设置 Blob 层](/rest/api/storageservices/set-blob-tier)
- 指定指定层的[Blob](/rest/api/storageservices/put-blob)
- 指定了层的[放置块列表](/rest/api/storageservices/put-block-list)
- [将 Blob 复制](/rest/api/storageservices/copy-blob) 到指定的层

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>启用软删除后删除 blob

启用 blob 软删除后，如果删除或覆盖已显式设置其层的基本 blob，则软删除的 blob 的任何以前版本或快照都将按完整内容长度计费。 有关 blob 版本控制和软删除如何协同工作的详细信息，请参阅 [blob 版本控制和软删除](versioning-overview.md#blob-versioning-and-soft-delete)。

下表描述了软删除的 blob 的计费行为，具体取决于启用还是禁用了版本控制。 启用版本控制后，在对 blob 进行软删除时，将创建新版本。 禁用版本控制后，软删除 blob 会创建软删除快照。

| 当你覆盖基本 blob 并显式设置其层时 .。。 | 然后，你需要支付 .。。 |
|-|-|
| 如果 blob 软删除和版本控制均已启用 | 完全内容长度的所有现有版本，与层无关。 |
| 如果启用了 blob 软删除但禁用了版本控制 | 所有现有的软删除快照（完全内容长度，而不考虑层）。 |

## <a name="next-steps"></a>后续步骤

- [Blob 版本控制](versioning-overview.md)
- [在 .NET 中创建和管理 blob 快照](snapshots-manage-dotnet.md)
- [通过递增快照备份 Azure 非托管 VM 磁盘](../../virtual-machines/windows/incremental-snapshots.md)
