---
title: Blob 版本控制
titleSuffix: Azure Storage
description: Blob 存储版本控制自动维护对象的以前版本，并使用时间戳标识它们。 如果错误地修改或删除了数据，则可以还原以前版本的 blob 以恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 20e48640d52fba7b3262014c2e84cfc56c7110cc
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767228"
---
# <a name="blob-versioning"></a>Blob 版本控制

可以启用 Blob 存储版本控制来自动维护对象的以前版本。  启用 blob 版本控制后，如果错误地修改或删除了数据，则可以还原该 blob 的早期版本以恢复数据。

Blob 版本控制在存储帐户上启用，适用于存储帐户中的所有 blob。 为存储帐户启用 blob 版本控制后，Azure 存储将自动维护存储帐户中每个 blob 的版本。

Microsoft 建议使用 blob 版本控制来维护以前版本的 blob，以实现高级数据保护。 如果可能，请使用 blob 版本控制而不是 blob 快照来维护以前的版本。 Blob 快照提供了类似的功能，因为它们维护更早版本的 blob，但快照必须由你的应用程序手动维护。

若要了解如何启用 blob 版本控制，请参阅 [启用和管理 blob 版本控制](versioning-enable.md)。

> [!IMPORTANT]
> Blob 版本控制无法帮助你从意外删除存储帐户或容器中恢复。 若要防止意外删除存储帐户，请在存储帐户资源上配置 **CannotDelete** 锁。 有关锁定 Azure 资源的详细信息，请参阅 [锁定资源以防止意外更改](../../azure-resource-manager/management/lock-resources.md)。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-versioning-works"></a>Blob 版本控制的工作原理

版本捕获 blob 在给定时间点的状态。 为存储帐户启用 blob 版本控制后，每次修改或删除 blob 时，Azure 存储会自动创建新版本的 blob。

当你创建启用了版本控制的 blob 时，新的 blob 是 (的 blob 的当前版本或基本 blob) 。 如果你随后修改了该 blob，Azure 存储将创建一个在修改之前捕获 blob 状态的版本。 修改后的 blob 将成为新的当前版本。 每次修改 blob 时都会创建一个新版本。 一个 blob 最多可以有1000个关联版本。

当删除启用了版本控制的 blob 时，Azure 存储将创建一个在删除之前捕获 blob 状态的版本。 然后，将删除 blob 的当前版本，但 blob 的版本将保持不变，以便可以在需要时重新创建。 

Blob 版本是不可变的。 不能修改现有 blob 版本的内容或元数据。

Blob 版本控制可用于常规用途 v2、块 blob 和 Blob 存储帐户。 当前不支持启用了层次结构命名空间以与 Azure Data Lake Storage Gen2 一起使用的存储帐户。 

Azure 存储 REST API 版本2019-10-10 及更高版本支持 blob 版本控制。

### <a name="version-id"></a>版本 ID

每个 blob 版本都由一个版本 ID 标识。 版本 ID 的值是写入或更新 blob 时的时间戳。 版本 ID 在创建版本时被分配。

您可以通过提供其版本 ID 对特定版本的 blob 执行读取或删除操作。 如果省略版本 ID，则该操作将针对基本 blob)  (的当前版本。

当你调用写操作来创建或修改 blob 时，Azure 存储将在响应中返回 *x ms 版本 id* 标头。 此标头包含写入操作创建的 blob 的当前版本的版本 ID。

版本 ID 在版本的生存期内保持不变。

### <a name="versioning-on-write-operations"></a>编写操作的版本控制

启用 blob 版本控制时，对 blob 的每个写入操作都会创建一个新版本。 写入操作包括 [Put blob](/rest/api/storageservices/put-blob)、 [put 块列表](/rest/api/storageservices/put-block-list)、 [复制 Blob](/rest/api/storageservices/copy-blob)和 [Set blob 元数据](/rest/api/storageservices/set-blob-metadata)。

如果写入操作创建新的 blob，则生成的 blob 为 blob 的当前版本。 如果写入操作修改了现有 blob，则会在更新后的 blob （即当前版本）中捕获新数据，Azure 存储将创建一个保存 blob 先前状态的版本。

为简单起见，本文中所示的关系图以简单整数值的形式显示版本 ID。 事实上，版本 ID 是一个时间戳。 当前版本以蓝色显示，并且以前的版本显示为灰色。

下图显示了写入操作如何影响 blob 版本。 创建 blob 时，该 blob 是当前版本。 当修改相同的 blob 时，将创建新版本来保存 blob 的先前状态，并且更新后的 blob 将成为当前版本。

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="显示写入操作如何影响版本控制 blob 的关系图。":::

> [!NOTE]
> 在为存储帐户启用版本控制之前创建的 blob 没有版本 ID。 当修改该 blob 时，修改后的 blob 将成为当前版本，并创建一个版本以在更新之前保存 blob 的状态。 为版本分配的是其创建时间的版本 ID。

### <a name="versioning-on-delete-operations"></a>删除操作的版本控制

当你删除某个 blob 时，该 blob 的当前版本将成为以前的版本，并且会删除基本 blob。 删除 blob 后，将保留 blob 的所有现有版本。

在没有版本 ID 的情况下调用 [删除 blob](/rest/api/storageservices/delete-blob) 操作会删除基本 Blob。 若要删除特定版本，请在 "删除" 操作上提供该版本的 ID。

下图显示了对版本控制的 blob 执行删除操作的影响：

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="显示写入操作如何影响版本控制 blob 的关系图。":::

将新数据写入 blob 会创建新版本的 blob。 不会影响任何现有版本，如下图所示。

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="显示写入操作如何影响版本控制 blob 的关系图。":::

### <a name="blob-types"></a>Blob 类型

为存储帐户启用 blob 版本控制后，块 blob 上的所有写入和删除操作都将触发新版本的创建， [Put 块](/rest/api/storageservices/put-block) 操作除外。

对于页 blob 和追加 blob，仅编写和删除操作的一部分会触发版本的创建。 这些操作包括：

- [放置 Blob](/rest/api/storageservices/put-blob)
- [放置块列表](/rest/api/storageservices/put-block-list)
- [删除 Blob](/rest/api/storageservices/delete-blob)
- [设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)
- [复制 Blob](/rest/api/storageservices/copy-blob)

以下操作不会触发新版本的创建。 若要从这些操作捕获更改，请执行手动快照：

- [将页面](/rest/api/storageservices/put-page) (页面 blob) 
- [追加块](/rest/api/storageservices/append-block) (追加 blob) 

Blob 的所有版本必须具有相同的 blob 类型。 如果 blob 具有以前的版本，则不能使用其他类型覆盖一种类型的 blob，除非先删除 blob 及其所有版本。

### <a name="access-tiers"></a>访问层级

可以通过调用 " [设置 Blob 层](/rest/api/storageservices/set-blob-tier) " 操作将块 blob 的任何版本（包括当前版本）移动到不同的 blob 访问层。 可以通过将较旧版本的 blob 移动到冷层或存档层来利用更低的容量定价。 有关详细信息，请参阅 [Azure Blob 存储： "热"、"冷" 和 "存档" 访问层](storage-blob-storage-tiers.md)。

若要将块 blob 自动移动到相应的层，请使用 blob 生命周期管理。 有关生命周期管理的详细信息，请参阅 [管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)。

## <a name="enable-or-disable-blob-versioning"></a>启用或禁用 blob 版本控制

若要了解如何启用或禁用 blob 版本控制，请参阅 [启用和管理 blob 版本控制](versioning-enable.md)。

禁用 blob 版本控制不会删除现有的 blob、版本或快照。 关闭 blob 版本控制后，任何现有版本都将在存储帐户中保持可访问状态。 不会再创建新版本。

如果在存储帐户上禁用了版本控制后创建或修改了 blob，则覆盖 blob 将创建新版本。 更新的 blob 不再是当前版本，并且没有版本 ID。 对 blob 的所有后续更新都将覆盖其数据，而不会保存以前的状态。

禁用版本控制后，可以使用版本 ID 读取或删除版本。 禁用版本控制后，还可以列出 blob 的版本。

下图显示了在禁用版本控制后修改 blob 的方式创建了一个不是版本控制的 blob。 与 blob 关联的任何现有版本将保持不变。

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="显示写入操作如何影响版本控制 blob 的关系图。":::

## <a name="blob-versioning-and-soft-delete"></a>Blob 版本控制和软删除

Blob 版本控制和 blob 软删除可协同工作，为你提供最佳的数据保护。 启用软删除后，需指定 Azure 存储应保留软删除 blob 的时间。 任何软删除的 blob 版本将保留在系统中，并可在软删除保留期内撤消删除。 有关 blob 软删除的详细信息，请参阅 [Azure 存储 blob 的软删除](storage-blob-soft-delete.md)。

### <a name="deleting-a-blob-or-version"></a>删除 blob 或版本

软删除为删除 blob 版本提供附加保护。 如果在存储帐户上同时启用了版本控制和软删除，则删除 blob 时，Azure 存储会创建新版本，以便在删除之前立即保存 blob 的状态并删除当前版本。 不会软删除新版本，并且在软删除保留期到期时不会删除。

删除 blob 的以前版本后，版本会被软删除。 软删除的版本将在存储帐户的 "软删除" 设置中指定的保留期内保留，并且在软删除保留期到期时永久删除。

若要删除以前版本的 blob，请通过指定版本 ID 将其显式删除。

下图显示了在删除 blob 或 blob 版本时会发生什么情况。

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="显示写入操作如何影响版本控制 blob 的关系图。":::

如果在存储帐户上同时启用了版本控制和软删除，则在修改或删除 blob 或 blob 版本时，不会创建软删除的快照。

### <a name="restoring-a-soft-deleted-version"></a>还原软删除版本

在软删除保留期生效时，可以通过对版本调用 " [撤消删除 blob](/rest/api/storageservices/undelete-blob) " 操作来还原软删除的 blob 版本。 " **撤消删除 blob** " 操作还原 blob 的所有软删除版本。

还原包含 " **撤消删除 Blob** " 操作的软删除版本不会将任何版本提升为当前版本。 要还原当前版本，请首先还原所有软删除的版本，然后使用 [复制 Blob](/rest/api/storageservices/copy-blob) 操作复制以前的版本以还原 Blob。

下图显示了如何还原软删除的 blob 版本与删除了 **blob** 操作，以及如何通过 **复制 blob** 操作还原 blob 的当前版本。

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="显示写入操作如何影响版本控制 blob 的关系图。":::

在软删除保留期结束之后，将永久删除任何软删除的 blob 版本。

## <a name="blob-versioning-and-blob-snapshots"></a>Blob 版本控制和 blob 快照

Blob 快照是在特定时间点拍摄的 blob 的只读副本。 Blob 快照和 blob 版本类似，但快照是由你或你的应用程序手动创建的，而 blob 版本是在为存储帐户启用 blob 版本控制时自动创建的。

> [!IMPORTANT]
> Microsoft 建议在启用 blob 版本控制后，也更新应用程序以停止拍摄块 blob 的快照。 如果为你的存储帐户启用了版本控制，则会捕获所有版本的块 blob 更新和删除。 如果启用了 blob 版本控制，则拍摄快照不会向块 blob 数据提供任何其他保护，并可能会增加成本和应用程序复杂性。

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>启用版本控制时快照 blob

尽管不建议这样做，但也可以拍摄也是版本控制的 blob 的快照。 如果在启用版本控制时无法更新应用程序以停止拍摄 blob 快照，你的应用程序可以支持快照和版本。

拍摄版本控制的 blob 的快照时，会在创建快照的同时创建一个新版本。 在拍摄快照时，还会创建一个新的当前版本。

下图显示拍摄已版本控制的 blob 的快照时会发生的情况。 在关系图中，版本 ID 为2和3的 blob 版本和快照包含相同的数据。

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="显示写入操作如何影响版本控制 blob 的关系图。":::

## <a name="authorize-operations-on-blob-versions"></a>对 blob 版本的操作授权

您可以使用以下方法之一来授予对 blob 版本的访问权限：

- 通过使用 azure 基于角色的访问控制 (Azure RBAC) 授予 Azure Active Directory (Azure AD) 安全主体的权限。 Microsoft 建议使用 Azure AD，以实现更高的安全性和易用性。 有关将 Azure AD 与 blob 操作一起使用的详细信息，请参阅 [使用 Azure Active Directory 授予对 blob 和队列的访问权限](../common/storage-auth-aad.md)。
- 通过使用共享访问签名 (SAS) 委托对 blob 版本的访问。 指定已签名资源类型 `bv` （表示 blob 版本）的版本 ID，以创建针对特定版本的操作的 SAS 令牌。 有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。
- 通过使用帐户访问密钥向使用共享密钥的 blob 版本授权操作。 有关详细信息，请参阅[通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key)。

Blob 版本控制用于保护数据免遭意外或恶意删除。 若要增强保护，删除 blob 版本需要特殊权限。 以下各节描述了删除 blob 版本所需的权限。

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>用于删除 blob 版本的 Azure RBAC 操作

下表显示了哪些 Azure RBAC 操作支持删除 blob 或 blob 版本。

| 说明 | Blob 服务操作 | 需要 Azure RBAC 数据操作 | Azure 内置角色支持 |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| 正在删除 blob 的当前版本 | 删除 Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | 存储 Blob 数据参与者 |
| 删除版本 | 删除 Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action** | 存储 Blob 数据所有者 |

### <a name="shared-access-signature-sas-parameters"></a> (SAS) 参数的共享访问签名

Blob 版本的已签名资源为 `bv` 。 有关详细信息，请参阅 [创建服务 sas](/rest/api/storageservices/create-service-sas) 或 [创建用户委托 SAS](/rest/api/storageservices/create-user-delegation-sas)。

下表显示了在 SAS 上删除 blob 版本所需的权限。

| **权限** | **URI 符号** | **允许的操作** |
|----------------|----------------|------------------------|
| 删除         | x              | 删除 blob 版本。 |

## <a name="pricing-and-billing"></a>定价和计费

启用 blob 版本控制可能会导致帐户产生额外的数据存储费用。 在设计应用程序时，有必要了解在哪些情况下会产生这些费用，以便最大程度地减少费用。

Blob 版本（如 blob 快照）按与活动数据相同的费率进行计费。 如何对版本进行计费取决于是否已为基本 blob 或其任何版本 (或快照) 显式设置层。 有关 blob 层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](storage-blob-storage-tiers.md)。

如果未更改 blob 或版本的层，则会对该 blob 的唯一数据块、其版本以及它可能具有的任何快照进行计费。 有关详细信息，请参阅[在未显式设置 blob 层级时进行计费](#billing-when-the-blob-tier-has-not-been-explicitly-set)。

如果更改了 blob 或版本的层，则会对整个对象计费，而不考虑 blob 和版本是否最终在同一层中。 有关详细信息，请参阅[在显式设置了 blob 层级时进行计费](#billing-when-the-blob-tier-has-been-explicitly-set)。

> [!NOTE]
> 为经常被覆盖的数据启用版本控制可能会增加存储容量费用，并在列出操作期间增加延迟。 若要缓解这些问题，请将频繁覆盖的数据存储在禁用了版本控制的单独存储帐户中。

有关 blob 快照的计费详细信息，请参阅 [blob 快照](snapshots-overview.md)。

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>在未显式设置 blob 层级时进行计费

如果没有为基本 blob 或其任何版本显式设置 blob 层，则会为 blob、其版本以及它可能拥有的任何快照支付唯一的块或页的费用。 在 blob 及其版本间共享的数据只收费一次。 在更新 blob 时，基础 blob 中的数据与其分离其版本中存储的数据，并按块或页面对唯一数据收费。

在替换块 Blob 中的某个块后，会将该块作为唯一块进行收费。 即使该块具有的块 ID 和数据与先前版本中的数据块 ID 和数据相同也是如此。 再次提交块后，它将从以前版本中的对应与其分离，并向你收取其数据的费用。 对于使用相同数据更新的页 Blob 中的页面来说，情况也是如此。

Blob 存储无法确定两个块是否包含相同的数据。 每个上传和提交的块均被视为唯一的块，即使它具有相同的数据和块 ID 也是如此。 由于唯一的块会产生费用，因此请务必记住，如果启用了版本控制，则更新 blob 将导致附加的唯一块和额外费用。

启用 blob 版本控制后，在块 blob 上调用更新操作，以使它们更新的块数目越少。 允许对块进行精细控制的写入操作是[放置块](/rest/api/storageservices/put-block)和[放置块列表](/rest/api/storageservices/put-block-list)。 另一方面，[放置 Blob](/rest/api/storageservices/put-blob) 操作会替换 blob 的全部内容，因此可能会导致额外的费用。

下面的方案演示了在未显式设置 blob 层时，块 blob 及其版本的计费方式。

#### <a name="scenario-1"></a>方案 1

在方案1中，blob 具有以前的版本。 Blob 在创建版本后未更新，因此仅对唯一块1、2和3产生费用。

![图1：显示基本 blob 和以前版本中的唯一块的计费。](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>方案 2

在方案2中，已更新 blob) 中的一个块 (块3。 即使更新的块包含相同的数据和 ID，它也与以前版本中的块3不同。 因此，帐户需要为四个块支付费用。

![图2：显示基本 blob 和以前版本中的唯一块的计费。](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>方案 3

在方案3中，已更新 blob，但版本未更新。 块3已替换为基础 blob 中的块4，但先前版本仍反映块3。 因此，帐户需要为四个块支付费用。

![图3：显示基本 blob 和以前版本中的唯一块的计费。](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>方案 4

在方案 4 中，已完全更新基本 Blob，并且其中不包含任何原始块。 因此，将对基本 blob 中的所有八个唯一块进行收费，在 &mdash; 以前的版本中对此帐户进行收费。 如果使用 [Put blob](/rest/api/storageservices/put-blob) 操作写入 blob，则会发生这种情况，因为它会替换基本 blob 的全部内容。

![图4：显示基本 blob 和以前版本中的唯一块的计费。](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>在显式设置了 blob 层级时进行计费

如果已显式设置 blob 或版本 (或快照) 的 blob 层，则会为新层中的对象的完整内容长度收费，而不考虑它是否与原始层中的对象共享块。 你还需要为原始层级中最早版本的完整内容长度付费。 保留在原始层中的任何其他版本或快照都按其可能共享的唯一块收费，如 [未显式设置 blob 层时计费](#billing-when-the-blob-tier-has-not-been-explicitly-set)中所述。

#### <a name="moving-a-blob-to-a-new-tier"></a>将 blob 移到新层级

下表描述了将 blob 或版本移到新层时的计费行为。

| 当显式设置 blob 层时 .。。 | 你需要为以下项付费 |
|-|-|
| 具有以前版本的基本 blob | 新层中的基本 blob 和原始层中的最早版本，以及其他版本中的任何唯一块。<sup>1</sup> |
| 具有以前版本和快照的基本 blob | 新层中的基本 blob、原始层中的最早版本和原始层中的最早快照，以及其他版本或快照中的任何唯一块<sup>1</sup>。 |
| 以前的版本 | 新层中的版本和原始层中的基础 blob 以及其他版本中的任何唯一块。<sup>1</sup> |

<sup>1</sup>如果其他以前的版本或快照尚未从其原始层中移出，这些版本或快照会根据它们所包含的唯一块的数量收费，如 [未显式设置 blob 层的计费](#billing-when-the-blob-tier-has-not-been-explicitly-set)中所述。

下图说明了在将版本控制的 blob 移动到不同层时，如何对对象计费。

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="显示写入操作如何影响版本控制 blob 的关系图。":::

无法撤消为 blob、版本或快照显式设置层。 如果你将 blob 移动到新层级，然后将其移回其原始层级，则即使该对象与原始层级中的其他对象共享块，也会针对该对象的完整内容长度向你收费。

显式设置 blob、版本或快照层的操作包括：

- [设置 Blob 层](/rest/api/storageservices/set-blob-tier)
- 在指定了层级的情况下[放置 Blob](/rest/api/storageservices/put-blob)
- 在指定了层级的情况下[放置块列表](/rest/api/storageservices/put-block-list)
- 在指定了层级的情况下[复制 Blob](/rest/api/storageservices/copy-blob)

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>在启用了软删除的情况下删除 blob

启用 blob 软删除后，如果删除或覆盖已显式设置其层的基本 blob，则会按完整内容长度对任何以前版本的软删除 blob 进行计费。 有关 blob 版本控制和软删除如何协同工作的详细信息，请参阅 [blob 版本控制和软删除](#blob-versioning-and-soft-delete)。

下表描述了软删除的 blob 的计费行为，具体取决于启用还是禁用了版本控制。 启用版本控制后，在对 blob 进行软删除时，将创建版本。 禁用版本控制后，软删除 blob 会创建软删除快照。

| 当你覆盖基本 blob 并显式设置其层时 .。。 | 你需要为以下项付费 |
|-|-|
| 如果 blob 软删除和版本控制均已启用 | 完全内容长度的所有现有版本，与层无关。 |
| 如果启用了 blob 软删除但禁用了版本控制 | 所有现有软删除快照（按完整内容长度），不考虑层级。 |

## <a name="see-also"></a>请参阅

- [启用和管理 blob 版本控制](versioning-enable.md)
- [创建 blob 的快照](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Azure 存储 Blob 的软删除](storage-blob-soft-delete.md)
