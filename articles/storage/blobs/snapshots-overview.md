---
title: Blob 快照
titleSuffix: Azure Storage
description: 了解如何在指定时刻及时创建 blob 的只读快照以备份 blob 数据。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 26646ad5ae42d2fa301a7f894084a78a64c73bd4
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884665"
---
# <a name="blob-snapshots"></a>Blob 快照

快照是在某一时间点拍摄的只读版本的 Blob。

> [!NOTE]
> Blob 版本控制（预览版）提供了另一种方法来维护 blob 的历史副本。 有关详细信息，请参阅[Blob 版本控制（预览）](versioning-overview.md)。

## <a name="about-blob-snapshots"></a>关于 Blob 快照

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Blob 的快照与其基本 Blob 相同，不过，Blob URI 的后面追加了一个 **DateTime** 值，用于指示快照的生成时间。 例如，如果页 Blob URI 为 `http://storagesample.core.blob.windows.net/mydrives/myvhd`，则快照 URI 将类似于 `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`。

> [!NOTE]
> 所有快照共享基本 Blob 的 URI。 基本 Blob 与快照之间的唯一区别体现在追加的 **DateTime** 值。
>

一个 Blob 可以有任意数目的快照。 快照会一直保留，直到被显式删除，这意味着快照的生存期不能长于其基本 Blob。 可以枚举与基本 Blob 关联的快照，以跟踪当前快照。

创建 Blob 的快照时，会将该 Blob 的系统属性复制到具有相同值的快照。 基本 Blob 的元数据也会复制到快照，除非创建快照时为其指定了不同的元数据。 创建快照后，可以读取、复制或删除它，但无法修改它。

任何与基本 Blob 关联的租约都不会影响快照。 无法获取快照上的租约。

VHD 文件用于存储 VM 磁盘的当前信息和状态。 可以将磁盘从 VM 分离或者关闭 VM，然后拍摄其 VHD 文件的快照。 可以在以后使用该快照文件检索该时间点的 VHD 文件并重新创建 VM。

## <a name="understand-how-snapshots-accrue-charges"></a>了解快照如何产生费用

创建快照（它是 Blob 的只读副本）会导致帐户产生额外的数据存储费用。 在设计应用程序时，务必要注意在哪些情况下会产生这些费用，以便能最大限度地降低成本。

### <a name="important-billing-considerations"></a>重要计费注意事项

以下列表包含创建快照时要考虑的要点。

- 不管唯一的块或页是在 Blob 还是快照中，存储帐户都会产生费用。 在更新快照所基于的 Blob 之前，帐户将不会就与 Blob 关联的快照产生额外费用。 更新基本 Blob 后，它与其快照分离。 发生这种情况时，需要支付每个 Blob 或快照中唯一块或页的费用。
- 在替换块 Blob 中的某个块后，会将该块作为唯一块进行收费。 即使该块具有的块 ID 和数据与它在快照中所具有的 ID 和数据相同也是如此。 重新提交块后，它将偏离它在任何快照中的对应部分，并且要为数据支付费用。 对于使用相同的数据更新的页 Blob 中的页面，也是如此。
- 通过调用 [UploadFromFile] [dotnet_UploadFromFile]、[UploadText] [dotnet_UploadText]、[UploadFromStream] [dotnet_UploadFromStream] 或 [UploadFromByteArray] [dotnet_UploadFromByteArray] 方法替换块 blob 将替换 blob 中的所有块。 如果有与该 Blob 关联的快照，则基本 Blob 和快照中的所有块现在将发生偏离，并且你需为这两个 Blob 中的所有块支付费用。 即使基本 Blob 和快照中的数据保持相同也是如此。
- Azure BLOB 服务无法确定这两个块是否包含相同的数据。 每个上传和提交的块均被视为唯一的快，即使它具有相同的数据和块 ID 也是如此。 由于唯一的块会产生费用，因此请务必考虑到更新具有快照的 Blob 将导致产生其他唯一块和额外费用。

### <a name="minimize-cost-with-snapshot-management"></a>使用快照管理最大限度地降低成本

建议细致管理快照以避免产生额外费用。 可遵循这些最佳做法来帮助最大限度地减少快照存储成本：

- 除非应用程序设计需要保留与 Blob 关联的快照，否则请在更新 Blob 时删除并重新创建这些快照，即使你使用相同的数据进行更新也是如此。 通过删除并重新创建 Blob 的快照，可以确保 Blob 和快照不会发生偏离。
- 如果要保留 blob 的快照，请避免调用 [UploadFromFile] [dotnet_UploadFromFile]、[UploadText] [dotnet_UploadText]、[UploadFromStream] [dotnet_UploadFromStream] 或 [UploadFromByteArray] [dotnet_UploadFromByteArray] 来更新 blob。 这些方法将替换 Blob 中的所有块，导致基本 Blob 及其快照发生明显偏离。 改为使用 [PutBlock] [dotnet_PutBlock] 和 [PutBlockList] [dotnet_PutBlockList] 方法更新块的最少数目。

### <a name="snapshot-billing-scenarios"></a>快照计费方案

下列方案说明了块 Blob 及其快照将如何产生费用。

#### <a name="scenario-1"></a>方案 1

在方案 1 中，基本 Blob 自创建快照后未进行更新，因此仅唯一块 1、2、3 会产生费用。

![Azure 存储资源](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>方案 2

在方案 2 中，已更新基本 Blob，但未更新快照。 已更新块 3，即使它包含相同的数据和 ID，它也与快照中的块 3 不同。 因此，帐户需要为四个块支付费用。

![Azure 存储资源](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>方案 3

在方案 3 中，已更新基本 Blob，但未更新快照。 块 3 已替换为基础 Blob 中的块 4，但快照仍反映块 3。 因此，帐户需要为四个块支付费用。

![Azure 存储资源](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>方案 4

在方案 4 中，已完全更新基本 Blob，并且其中不包含任何原始块。 因此，帐户需要为所有八个唯一块支付费用。 如果使用 [UploadFromFile] [dotnet_UploadFromFile]、[UploadText] [dotnet_UploadText]、[UploadFromStream] [dotnet_UploadFromStream] 或 [UploadFromByteArray] [dotnet_UploadFromByteArray] 等 update 方法，则会发生这种情况，因为这些方法将替换 blob 的所有内容。

![Azure 存储资源](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>后续步骤

- [在 .NET 中创建和管理 blob 快照](snapshots-manage-dotnet.md)
- [使用递增快照备份 Azure 非托管 VM 磁盘](../../virtual-machines/windows/incremental-snapshots.md)
