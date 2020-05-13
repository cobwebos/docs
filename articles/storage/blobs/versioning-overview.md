---
title: Blob 版本控制（预览）
titleSuffix: Azure Storage
description: Blob 存储版本控制（预览版）可自动维护对象的以前版本，并使用时间戳标识它们。 如果错误地修改或删除了数据，则可以还原以前版本的 blob 以恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 7e4bc74a51e3d6b19957bdd12512e18fa594c811
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123830"
---
# <a name="blob-versioning-preview"></a>Blob 版本控制（预览）

可以启用 Blob 存储版本控制（预览版）来自动维护对象的以前版本。  启用 blob 版本控制后，如果错误地修改或删除了数据，则可以还原该 blob 的早期版本以恢复数据。

Blob 版本控制在存储帐户上启用，适用于存储帐户中的所有 blob。 为存储帐户启用 blob 版本控制后，Azure 存储将自动维护存储帐户中每个 blob 的版本。

Microsoft 建议使用 blob 版本控制来维护以前版本的 blob，以实现高级数据保护。 如果可能，请使用 blob 版本控制而不是 blob 快照来维护以前的版本。 Blob 快照提供了类似的功能，因为它们维护更早版本的 blob，但快照必须由你的应用程序手动维护。

> [!IMPORTANT]
> Blob 版本控制无法帮助你从意外删除存储帐户或容器中恢复。 若要防止意外删除存储帐户，请在存储帐户资源上配置**CannotDelete**锁。 有关锁定 Azure 资源的详细信息，请参阅[锁定资源以防止意外更改](../../azure-resource-manager/management/lock-resources.md)。

## <a name="how-blob-versioning-works"></a>Blob 版本控制的工作原理

版本捕获 blob 在给定时间点的状态。 为存储帐户启用 blob 版本控制后，每次修改或删除 blob 时，Azure 存储会自动创建新版本的 blob。

创建启用了版本控制的 blob 时，新的 blob 是该 blob 的当前版本（或基 blob）。 如果你随后修改了该 blob，Azure 存储将创建一个在修改之前捕获 blob 状态的版本。 修改后的 blob 将成为新的当前版本。 每次修改 blob 时都会创建一个新版本。

当删除启用了版本控制的 blob 时，Azure 存储将创建一个在删除之前捕获 blob 状态的版本。 然后，将删除 blob 的当前版本，但 blob 的版本将保持不变，以便可以在需要时重新创建。 

Blob 版本是不可变的。 不能修改现有 blob 版本的内容或元数据。

### <a name="version-id"></a>版本 ID

每个 blob 版本都由一个版本 ID 标识。 版本 ID 的值是写入或更新 blob 时的时间戳。 版本 ID 在创建版本时被分配。

您可以通过提供其版本 ID 对特定版本的 blob 执行读取或删除操作。 如果省略版本 ID，此操作将针对当前版本（基 blob）进行操作。

当你调用写操作来创建或修改 blob 时，Azure 存储将在响应中返回*x ms 版本 id*标头。 此标头包含写入操作创建的 blob 的当前版本的版本 ID。

版本 ID 在版本的生存期内保持不变。

### <a name="versioning-on-write-operations"></a>编写操作的版本控制

启用 blob 版本控制时，对 blob 的每个写入操作都会创建一个新版本。 写入操作包括[Put blob](/rest/api/storageservices/put-blob)、 [put 块列表](/rest/api/storageservices/put-block-list)、[复制 Blob](/rest/api/storageservices/copy-blob)和[Set blob 元数据](/rest/api/storageservices/set-blob-metadata)。

如果写入操作创建新的 blob，则生成的 blob 为 blob 的当前版本。 如果写入操作修改了现有 blob，则会在更新后的 blob （即当前版本）中捕获新数据，Azure 存储将创建一个保存 blob 先前状态的版本。

为简单起见，本文中所示的关系图以简单整数值的形式显示版本 ID。 事实上，版本 ID 是一个时间戳。 当前版本以蓝色显示，并且以前的版本显示为灰色。

下图显示了写入操作如何影响 blob 版本。 创建 blob 时，该 blob 是当前版本。 当修改相同的 blob 时，将创建新版本来保存 blob 的先前状态，并且更新后的 blob 将成为当前版本。

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="显示写入操作如何影响版本控制 blob 的关系图":::

> [!NOTE]
> 在为存储帐户启用版本控制之前创建的 blob 没有版本 ID。 当修改该 blob 时，修改后的 blob 将成为当前版本，并创建一个版本以在更新之前保存 blob 的状态。 为版本分配的是其创建时间的版本 ID。

### <a name="versioning-on-delete-operations"></a>删除操作的版本控制

当你删除某个 blob 时，该 blob 的当前版本将成为以前的版本，并且会删除基本 blob。 删除 blob 后，将保留 blob 的所有现有版本。

在没有版本 ID 的情况下调用[删除 blob](/rest/api/storageservices/delete-blob)操作会删除基本 Blob。 若要删除特定版本，请在 "删除" 操作上提供该版本的 ID。

下图显示了对版本控制的 blob 执行删除操作的影响：

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="显示已进行版本控制的 blob 的删除的关系图":::

将新数据写入 blob 会创建新版本的 blob。 不会影响任何现有版本，如下图所示。

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="显示在删除后重新创建版本控制的 blob 的关系图":::

### <a name="blob-types"></a>Blob 类型

为存储帐户启用 blob 版本控制后，块 blob 上的所有写入和删除操作都将触发新版本的创建， [Put 块](/rest/api/storageservices/put-block)操作除外。

对于页 blob 和追加 blob，仅编写和删除操作的一部分会触发版本的创建。 这些操作包括：

- [放置 Blob](/rest/api/storageservices/put-blob)
- [放置块列表](/rest/api/storageservices/put-block-list)
- [删除 Blob](/rest/api/storageservices/delete-blob)
- [设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)
- [复制 Blob](/rest/api/storageservices/copy-blob)

以下操作不会触发新版本的创建。 若要从这些操作捕获更改，请执行手动快照：

- [Put 页](/rest/api/storageservices/put-page)（页 blob）
- [追加块](/rest/api/storageservices/append-block)（追加 blob）

Blob 的所有版本必须具有相同的 blob 类型。 如果 blob 具有以前的版本，则不能使用其他类型覆盖一种类型的 blob，除非先删除 blob 及其所有版本。

### <a name="access-tiers"></a>访问层级

可以通过调用 "[设置 Blob 层](/rest/api/storageservices/set-blob-tier)" 操作将块 blob 的任何版本（包括当前版本）移动到不同的 blob 访问层。 可以通过将较旧版本的 blob 移动到冷层或存档层来利用更低的容量定价。 有关详细信息，请参阅[Azure Blob 存储： "热"、"冷" 和 "存档" 访问层](storage-blob-storage-tiers.md)。

若要将块 blob 自动移动到相应的层，请使用 blob 生命周期管理。 有关生命周期管理的详细信息，请参阅[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)。

## <a name="enable-or-disable-blob-versioning"></a>启用或禁用 blob 版本控制

若要了解如何启用或禁用 blob 版本控制，请参阅[启用或禁用 blob 版本控制](versioning-enable.md)。

禁用 blob 版本控制不会删除现有的 blob、版本或快照。 关闭 blob 版本控制后，任何现有版本都将在存储帐户中保持可访问状态。 不会再创建新版本。

如果在存储帐户上禁用了版本控制后创建或修改了 blob，则覆盖 blob 将创建新版本。 更新的 blob 不再是当前版本，并且没有版本 ID。 对 blob 的所有后续更新都将覆盖其数据，而不会保存以前的状态。

禁用版本控制后，可以使用版本 ID 读取或删除版本。 禁用版本控制后，还可以列出 blob 的版本。

下图显示了在禁用版本控制后修改 blob 的方式创建了一个不是版本控制的 blob。 与 blob 关联的任何现有版本将保持不变。

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="显示禁用版本控制后修改的基本 blob 的图示":::

## <a name="blob-versioning-and-soft-delete"></a>Blob 版本控制和软删除

Blob 版本控制和 blob 软删除可协同工作，为你提供最佳的数据保护。 启用软删除后，需指定 Azure 存储应保留软删除 blob 的时间。 任何软删除的 blob 版本将保留在系统中，并可在软删除保留期内撤消删除。 有关 blob 软删除的详细信息，请参阅[Azure 存储 blob 的软删除](storage-blob-soft-delete.md)。

### <a name="deleting-a-blob-or-version"></a>删除 blob 或版本

软删除为删除 blob 版本提供附加保护。 如果在存储帐户上同时启用了版本控制和软删除，则删除 blob 时，Azure 存储会创建新版本，以便在删除之前立即保存 blob 的状态并删除当前版本。 不会软删除新版本，并且在软删除保留期到期时不会删除。

删除 blob 的以前版本后，版本会被软删除。 软删除的版本将在存储帐户的 "软删除" 设置中指定的保留期内保留，并且在软删除保留期到期时永久删除。

若要删除以前版本的 blob，请通过指定版本 ID 将其显式删除。

下图显示了在删除 blob 或 blob 版本时会发生什么情况。

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="显示已启用软删除的版本删除的图示":::

如果在存储帐户上同时启用了版本控制和软删除，则在修改或删除 blob 或 blob 版本时，不会创建软删除的快照。

### <a name="restoring-a-soft-deleted-version"></a>还原软删除版本

在软删除保留期生效时，可以通过对版本调用 "[撤消删除 blob](/rest/api/storageservices/undelete-blob) " 操作来还原软删除的 blob 版本。 "**撤消删除 blob** " 操作还原 blob 的所有软删除版本。

还原包含 "**撤消删除 Blob** " 操作的软删除版本不会将任何版本提升为当前版本。 要还原当前版本，请首先还原所有软删除的版本，然后使用[复制 Blob](/rest/api/storageservices/copy-blob)操作复制以前的版本以还原 Blob。

下图显示了如何还原软删除的 blob 版本与删除了**blob**操作，以及如何通过**复制 blob**操作还原 blob 的当前版本。

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="显示如何还原软删除版本的关系图":::

在软删除保留期结束之后，将永久删除任何软删除的 blob 版本。

## <a name="blob-versioning-and-blob-snapshots"></a>Blob 版本控制和 blob 快照

Blob 快照是在特定时间点拍摄的 blob 的只读副本。 Blob 快照和 blob 版本类似，但快照是由你或你的应用程序手动创建的，而 blob 版本是在为存储帐户启用 blob 版本控制时自动创建的。

> [!IMPORTANT]
> Microsoft 建议在启用 blob 版本控制后，也更新应用程序以停止拍摄块 blob 的快照。 如果为你的存储帐户启用了版本控制，则会捕获所有版本的块 blob 更新和删除。 如果启用了 blob 版本控制，则拍摄快照不会向块 blob 数据提供任何其他保护，并可能会增加成本和应用程序复杂性。

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>启用版本控制时快照 blob

尽管不建议这样做，但也可以拍摄也是版本控制的 blob 的快照。 如果在启用版本控制时无法更新应用程序以停止拍摄 blob 快照，你的应用程序可以支持快照和版本。

拍摄版本控制的 blob 的快照时，会在创建快照的同时创建一个新版本。 在拍摄快照时，还会创建一个新的当前版本。

下图显示拍摄已版本控制的 blob 的快照时会发生的情况。 在关系图中，版本 ID 为2和3的 blob 版本和快照包含相同的数据。

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="显示已进行版本控制的 blob 的快照的关系图":::

## <a name="authorize-operations-on-blob-versions"></a>对 blob 版本的操作授权

您可以使用以下方法之一来授予对 blob 版本的访问权限：

- 通过使用基于角色的访问控制（RBAC）向 Azure Active Directory （Azure AD）安全主体授予权限。 Microsoft 建议使用 Azure AD，以实现更高的安全性和易用性。 有关将 Azure AD 与 blob 操作一起使用的详细信息，请参阅[使用 Azure Active Directory 授予对 blob 和队列的访问权限](../common/storage-auth-aad.md)。
- 使用共享访问签名（SAS）委派对 blob 版本的访问权限。 指定已签名资源类型 `bv` （表示 blob 版本）的版本 ID，以创建针对特定版本的操作的 SAS 令牌。 有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。
- 通过使用帐户访问密钥向使用共享密钥的 blob 版本授权操作。 有关详细信息，请参阅[通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key)。

Blob 版本控制用于保护数据免遭意外或恶意删除。 若要增强保护，删除 blob 版本需要特殊权限。 以下各节描述了删除 blob 版本所需的权限。

### <a name="rbac-action-to-delete-a-blob-version"></a>用于删除 blob 版本的 RBAC 操作

下表显示了哪些 RBAC 操作支持删除 blob 或 blob 版本。

| 说明 | Blob 服务操作 | 需要 RBAC 数据操作 | RBAC 内置角色支持 |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| 正在删除 blob 的当前版本 | 删除 Blob | **StorageAccounts/blobServices/容器/blob/删除/actionDeleting** | 存储 Blob 数据参与者 |
| 删除版本 | 删除 Blob | **StorageAccounts/blobServices/容器/blob/deleteBlobVersion/** | 存储 Blob 数据所有者 |

### <a name="shared-access-signature-sas-parameters"></a>共享访问签名（SAS）参数

Blob 版本的已签名资源为 `bv` 。 有关详细信息，请参阅[创建服务 sas](/rest/api/storageservices/create-service-sas)或[创建用户委托 SAS](/rest/api/storageservices/create-user-delegation-sas)。

下表显示了在 SAS 上删除 blob 版本所需的权限。

| **权限** | **URI 符号** | **允许的操作** |
|----------------|----------------|------------------------|
| 删除         | x              | 删除 blob 版本。 |

## <a name="about-the-preview"></a>关于此预览版

Blob 版本控制在以下区域提供预览版：

- 法国中部
- 加拿大东部
- 加拿大中部

此预览版仅用于非生产用途。

Azure 存储 REST API 版本2019-10-10 及更高版本支持 blob 版本控制。

### <a name="storage-account-support"></a>存储帐户支持

Blob 版本控制可用于以下类型的存储帐户：

- 常规用途 v2 存储帐户
- 块 Blob 存储帐户
- Blob 存储帐户

如果你的存储帐户是常规用途 v1 帐户，请使用 Azure 门户升级到常规用途 v2 帐户。 有关存储帐户的详细信息，请参阅[Azure 存储帐户概述](../common/storage-account-overview.md)。

当前不支持启用了层次结构命名空间以与 Azure Data Lake Storage Gen2 一起使用的存储帐户。

### <a name="register-for-the-preview"></a>注册预览版

若要在 blob 版本控制预览版中注册，请使用 PowerShell 或 Azure CLI 提交请求以便向你的订阅注册此功能。 批准请求后，可以使用任何新的或现有的常规用途 v2、Blob 存储或高级块 blob 存储帐户启用 blob 版本控制。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要向 PowerShell 注册，请调用[AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature)命令。

```powershell
# Register for blob versioning (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要注册 Azure CLI，请调用[az feature register](/cli/azure/feature#az-feature-register)命令。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name Versioning
```

---

### <a name="check-the-status-of-your-registration"></a>检查注册状态

若要检查注册状态，请使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要通过 PowerShell 检查注册状态，请调用[AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature)命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要查看 Azure CLI 的注册状态，请调用[az 功能](/cli/azure/feature#az-feature-show)命令。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name Versioning
```

---

## <a name="pricing-and-billing"></a>定价和计费

启用 blob 版本控制可能会导致帐户产生额外的数据存储费用。 在设计应用程序时，务必要注意在哪些情况下会产生这些费用，以便能最大限度地降低成本。

Blob 版本（如 blob 快照）按与活动数据相同的费率进行计费。 如果某个版本共享块或页与其基 blob，则只需为不在版本和基本 blob 之间共享的任何其他块或页面付费。

> [!NOTE]
> 为经常被覆盖的数据启用版本控制可能会增加存储容量费用，并在列出操作期间增加延迟。 若要缓解这些问题，请将频繁覆盖的数据存储在禁用了版本控制的单独存储帐户中。

### <a name="important-billing-considerations"></a>重要计费注意事项

启用 blob 版本控制时，请确保考虑以下几点：

- 存储帐户会对唯一的块或页产生费用，无论它们是在 blob 中还是在 blob 的以前版本中都是如此。 你的帐户在更新其所基于的 blob 之前，不会向与 blob 关联的版本收取额外费用。 更新 blob 后，它将从其以前的版本与其分离。 发生这种情况时，需要为每个 blob 或版本中的唯一块或页付费。
- 在替换块 Blob 中的某个块后，会将该块作为唯一块进行收费。 即使该块具有的块 ID 和数据与版本中的相同，也是如此。 再次提交块后，它将从任何版本的对应与其分离，并向你收取其数据的费用。 对于使用相同的数据更新的页 Blob 中的页面，也是如此。
- Blob 存储无法确定两个块是否包含相同的数据。 每个上传和提交的块均被视为唯一的快，即使它具有相同的数据和块 ID 也是如此。 由于唯一的块会产生费用，因此请务必考虑在启用版本控制时更新 blob 将导致附加的唯一块和额外费用。
- 启用 blob 版本控制后，在块 blob 上设计更新操作，以使它们更新的块数量越少。 允许对块进行精细控制的写入操作是[put](/rest/api/storageservices/put-block)块和[put 块列表](/rest/api/storageservices/put-block-list)。 另一方面， [Put Blob](/rest/api/storageservices/put-blob)操作将替换 Blob 的全部内容，因此可能会导致额外的费用。

### <a name="versioning-billing-scenarios"></a>版本计费方案

以下方案说明了块 blob 及其版本的计费方式。

#### <a name="scenario-1"></a>方案 1

在方案1中，blob 具有以前的版本。 Blob 在创建版本后未更新，因此仅对唯一块1、2和3产生费用。

![Azure 存储资源](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>方案 2

在方案2中，blob 中的一个块（关系图中的块3）已更新。 即使更新的块包含相同的数据和 ID，它也与以前版本中的块3不同。 因此，帐户需要为四个块支付费用。

![Azure 存储资源](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>方案 3

在方案3中，已更新 blob，但版本未更新。 块3已替换为基础 blob 中的块4，但先前版本仍反映块3。 因此，帐户需要为四个块支付费用。

![Azure 存储资源](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>方案 4

在方案 4 中，已完全更新基本 Blob，并且其中不包含任何原始块。 因此，将对基本 blob 中的所有八个唯一块进行收费，在 &mdash; 以前的版本中对此帐户进行收费。 如果使用 Put Blob 操作写入 blob，则会发生这种情况，因为它会替换基本 blob 的全部内容。

![Azure 存储资源](./media/versioning-overview/versions-billing-scenario-4.png)

## <a name="see-also"></a>另请参阅

- [启用 Blob 版本控制](versioning-enable.md)
- [创建 blob 的快照](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Azure 存储 Blob 的软删除](storage-blob-soft-delete.md)
