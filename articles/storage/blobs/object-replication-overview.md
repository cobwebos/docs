---
title: 对象复制概述（预览版）
titleSuffix: Azure Storage
description: 对象复制（预览版）在源存储帐户和目标帐户之间异步复制块 blob。 使用对象复制可最大程度地降低读取请求延迟，提高计算工作负载的效率，优化数据分发，并最大限度地降低成本。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2d8d4c369cef8bf996628e8c89a424f04dcdbe71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888071"
---
# <a name="object-replication-for-block-blobs-preview"></a>块 blob 的对象复制（预览版）

对象复制（预览版）在源存储帐户和目标帐户之间异步复制块 blob。 对象复制支持的部分方案包括：

- **最大程度地降低延迟。** 通过使客户端可以从物理位置更接近的区域使用数据，对象复制可以降低读取请求的延迟。
- **提高计算工作负载的效率。** 通过对象复制，计算工作负载可以在不同区域中处理相同的块 blob 集。
- **优化数据分发。** 您可以在单个位置处理或分析数据，然后仅将结果复制到其他区域。
- **优化成本。** 复制数据后，可以使用生命周期管理策略将数据转移到存档层，从而降低成本。

下图显示了对象复制如何将块 blob 从一个区域中的源存储帐户复制到两个不同区域中的目标帐户。

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="显示对象复制工作方式的图":::

若要了解如何配置对象复制，请参阅[配置对象复制（预览版）](object-replication-configure.md)。

## <a name="object-replication-policies-and-rules"></a>对象复制策略和规则

配置对象复制时，需要创建复制策略，以指定源存储帐户和目标帐户。 复制策略包括一个或多个规则，用于指定源容器和目标容器，并指明复制源容器中的哪些块 blob。

配置对象复制以后，Azure 存储会定期检查源帐户的更改源，并将所有写入或删除操作以异步方式复制到目标帐户。 复制延迟取决于要复制的块 blob 的大小。

> [!IMPORTANT]
> 因为块 blob 数据是以异步方式复制的，所以源帐户和目标帐户不会立即同步。目前没有关于将数据复制到目标帐户所需时间的 SLA。

### <a name="replications-policies"></a>复制策略

配置对象复制时，需要通过 Azure 存储资源提供程序针对源帐户和目标帐户创建复制策略。 复制策略通过策略 ID 进行标识。 源帐户和目标帐户的策略必须具有相同的策略 ID，才能进行复制。

存储帐户可用作最多两个目标帐户的源帐户。 并且目标帐户不能有两个以上的源帐户。 源帐户和目标帐户可能全都位于不同的区域。 可以配置单独的复制策略，以便将数据复制到每个目标帐户。

### <a name="replication-rules"></a>复制规则

复制规则指定 Azure 存储如何将 blob 从源容器复制到目标容器。 可以为每个复制策略最多指定 10 个复制规则。 每个规则都定义单个源和目标容器，并且每个源和目标容器只能在一个规则中使用。

创建复制规则时，默认情况下仅复制随后添加到源容器的新块 blob。 还可以指定复制新的块 blob 和现有块 blob，也可以定义自定义复制范围，以复制从指定的时间开始创建的块 blob。

也可以将一个或多个筛选器指定为复制规则的一部分，以按前缀筛选块 blob。 指定前缀时，只会将源容器中与该前缀匹配的 blob 复制到目标容器。

源和目标容器必须都存在，然后才能在规则中指定它们。 创建复制策略后，目标容器变为只读。 任何尝试写入目标容器的操作都会失败，错误代码为 409（冲突）。 但是，可以对目标容器中的 blob 调用 "[设置 Blob 层](/rest/api/storageservices/set-blob-tier)" 操作，以将其移动到 "存档" 层。 有关存档层的详细信息，请参阅[Azure Blob 存储： "热"、"冷" 和 "存档" 访问层](storage-blob-storage-tiers.md#archive-access-tier)。

## <a name="about-the-preview"></a>关于此预览版

仅对常规用途 v2 存储帐户支持对象复制。 在预览版中，以下区域中提供对象复制：

- 法国中部
- 加拿大东部
- 加拿大中部

源帐户和目标帐户必须都位于其中一个区域，才能使用对象复制。 这些帐户可以在两个不同的区域中。

在使用预览版时，在存储帐户之间复制数据不会产生额外的成本。

> [!IMPORTANT]
> 对象复制预览版仅用于非生产用途。 生产服务级别协议 (SLA) 当前不可用。

### <a name="prerequisites-for-object-replication"></a>对象复制的先决条件

对象复制需要启用以下 Azure 存储功能： 
- [更改源](storage-blob-change-feed.md)
- [版本管理](versioning-overview.md)

在配置对象复制之前，请先启用其先决条件。 必须在源帐户上启用更改源，并且必须同时在源帐户和目标帐户上启用 blob 版本控制。 有关如何启用上述功能的详细信息，请参阅以下文章：

- [启用和禁用更改源](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [启用和管理 blob 版本控制](versioning-enable.md)

在启用更改源和 blob 版本控制预览版之前，请务必注册这些预览版。

启用更改源和 blob 版本控制可能会产生额外的成本。 有关更多详细信息，请参阅 [Azure 存储定价页](https://azure.microsoft.com/pricing/details/storage/)。

### <a name="register-for-the-preview"></a>注册预览版

可以使用 PowerShell 或 Azure CLI 注册对象复制预览版。 确保也注册更改源和 blob 版本控制预览版（如果尚未这样做）。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 注册预览版，请运行以下命令：

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 注册预览版，请运行以下命令：

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>检查注册状态

可以使用 PowerShell 或 Azure CLI 来检查注册请求的状态。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 检查注册请求的状态，请运行以下命令：

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 检查注册请求的状态，请运行以下命令：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>提问或提供反馈

若要针对对象复制预览版提问或提供反馈，请通过 AzureStorageFeedback@microsoft.com 联系 Microsoft。 欢迎在 [Azure 存储反馈论坛](https://feedback.azure.com/forums/217298-storage)提出你对 Azure 存储的想法和建议。

## <a name="next-steps"></a>后续步骤

- [配置对象复制（预览版）](object-replication-configure.md)
- [Azure Blob 存储中的更改源支持（预览）](storage-blob-change-feed.md)
- [启用和管理 blob 版本控制](versioning-enable.md)
