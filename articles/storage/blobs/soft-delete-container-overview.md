---
title: '容器的软删除 (预览) '
titleSuffix: Azure Storage
description: 容器的软删除 (预览) 保护你的数据，以便在应用程序或其他存储帐户用户错误地修改或删除数据时，可以更轻松地恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 3c6aa408ab3e89c367fca0701773b35fb9b41bb8
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460792"
---
# <a name="soft-delete-for-containers-preview"></a>容器的软删除 (预览) 

容器 (的软删除) 保护数据不被意外或错误地修改或删除。 为存储帐户启用容器软删除后，在指定的时间段内，任何删除的容器及其内容将保留在 Azure 存储中。 在保留期内，你可以还原以前删除的容器和其中的任何 blob。

对于 blob 数据的端到端保护，Microsoft 建议启用以下数据保护功能：

- 容器软删除，以防止意外删除或覆盖容器。 若要了解如何启用容器软删除，请参阅 [启用和管理容器的软删除](soft-delete-container-enable.md)。
- Blob 软删除，以防止意外删除或覆盖单个 blob。 若要了解如何启用 blob 软删除，请参阅 [blob 的软删除](soft-delete-blob-overview.md)。
- Blob 版本控制，用于自动维护以前版本的 blob。 启用 blob 版本控制后，如果错误地修改或删除了数据，则可以还原该 blob 的早期版本以恢复数据。 若要了解如何启用 blob 版本控制，请参阅 [启用和管理 blob 版本控制](versioning-enable.md)。

> [!WARNING]
> 无法撤消删除存储帐户。 软删除无法防止删除存储帐户。 若要防止意外删除存储帐户，请在存储帐户资源上配置 **CannotDelete** 锁。 有关锁定 Azure 资源的详细信息，请参阅 [锁定资源以防止意外更改](../../azure-resource-manager/management/lock-resources.md)。

## <a name="how-container-soft-delete-works"></a>容器软删除的工作原理

启用容器软删除时，可以为已删除的容器指定介于1到365天之间的保留期。 默认保持期为 7 天。 在保留期内，可以通过调用 " **撤消删除容器** " 操作来恢复已删除的容器。

当你还原某个容器时，如果该名称尚未重复使用，则可以将其还原为原始名称。 如果已使用原始容器名称，则可以使用新名称还原容器。

保持期到期后，会从 Azure 存储中永久删除该容器，并且无法恢复该容器。 时钟在容器被删除时的保持期开始。 你可以随时更改保留期，但请记住，更新的保留期仅适用于新删除的容器。 根据删除容器时生效的保留期，将会永久删除以前删除的容器。

禁用容器软删除不会永久删除以前软删除的容器。 任何软删除的容器将在删除容器时生效的保留期到期时永久删除。

## <a name="about-the-preview"></a>关于此预览版

容器软删除在所有公共 Azure 区域中均以预览版提供。

> [!IMPORTANT]
> 容器软删除预览版仅供非生产使用。 生产服务级别协议 (SLA) 当前不可用。

Azure 存储 REST API 版本2019-12-12 及更高版本支持容器软删除。

### <a name="storage-account-support"></a>存储帐户支持

容器软删除可用于以下类型的存储帐户：

- 常规用途 v2 存储帐户
- 块 Blob 存储帐户
- Blob 存储帐户

如果你的存储帐户是常规用途 v1 帐户，请使用 Azure 门户升级到常规用途 v2 帐户。 有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。

还支持启用了分层命名空间以与 Azure Data Lake Storage Gen2 一起使用的存储帐户。

### <a name="register-for-the-preview"></a>注册预览版

若要为容器软删除注册预览，请使用 PowerShell 或 Azure CLI 提交请求以便向你的订阅注册此功能。 批准请求后，可以使用任何新的或现有的常规用途 v2、Blob 存储或高级块 Blob 存储帐户启用容器软删除。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要向 PowerShell 注册，请调用 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 命令。

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要注册 Azure CLI，请调用 [az feature register](/cli/azure/feature#az-feature-register) 命令。

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>检查注册状态

若要检查注册状态，请使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要通过 PowerShell 检查注册状态，请调用 [AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要查看 Azure CLI 的注册状态，请调用 [az 功能](/cli/azure/feature#az-feature-show) 命令。

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>定价和计费

启用容器软删除没有额外的费用。 软删除容器中的数据按与活动数据相同的费率进行计费。

## <a name="next-steps"></a>后续步骤

- [配置容器软删除](soft-delete-container-enable.md)
- [blob 的软删除](soft-delete-blob-overview.md)
- [Blob 版本控制](versioning-overview.md)
