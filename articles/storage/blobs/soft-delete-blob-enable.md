---
title: 启用和管理 blob 的软删除
titleSuffix: Azure Storage
description: 为 blob 启用软删除，以便在错误地修改或删除数据时更轻松地恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: cba56e699afed8178f93bceddf6bf32a242ed124
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020841"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>启用和管理 blob 的软删除

Blob 软删除可防止意外或错误地修改或删除数据。 为存储帐户启用 blob 软删除后，在指定的保持期内，在删除存储帐户中的 blob、blob 版本 (预览) 和快照时，可能会恢复这些 blob。

如果某个应用程序或其他存储帐户用户可能无意中修改或删除了数据，则 Microsoft 建议启用 blob 软删除。 本文介绍如何为 blob 启用软删除。 有关 blob 软删除的详细信息，请参阅 [blob 的软删除](soft-delete-blob-overview.md)。

若要了解如何同时为容器启用软删除，请参阅 [启用和管理容器的软删除](soft-delete-container-enable.md)。

## <a name="enable-blob-soft-delete"></a>启用 blob 软删除

# <a name="portal"></a>[门户](#tab/azure-portal)

使用 Azure 门户为存储帐户上的 blob 启用软删除：

1. 在 [Azure 门户](https://portal.azure.com/)中导航到存储帐户。
1. 在 " **Blob 服务**" 下找到 "**数据保护**" 选项。
1. 将 " **Blob 软删除** " 属性设置为 " *已启用*"。
1. 在 " **保留策略**" 下，指定软删除的 Blob 由 Azure 存储保留多长时间。
1. 保存所做更改。

![选择了数据保护 blob 服务的 Azure 门户的屏幕截图。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

若要查看软删除 blob，请选择“显示已删除 blob”复选框。

![突出显示 "显示已删除 blob" 选项的 "数据保护 blob 服务" 页的屏幕截图。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

若要查看给定 blob 的软删除快照，请选择相应 blob 并单击“查看快照”。

![突出显示 "查看快照" 选项的 "数据保护 blob 服务" 页的屏幕截图。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

请确保已选择“显示已删除快照”复选框。

![突出显示 "显示已删除 blob" 选项的 "视图快照" 页的屏幕截图。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

单击软删除 blob 或快照时，请注意新的 blob 属性。 这些属性指示删除对象的时间，以及 blob 或 blob 快照永久过期前的剩余天数。 如果软删除对象不是快照，还可选择撤销删除。

![软删除对象的详细信息的屏幕截图。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

请记住，撤销删除 blob 还将撤销删除所有相关快照。 若要撤销删除活动 blob 的软删除快照，请单击相应 blob 并选择“撤销删除所有快照”。

![软删除 blob 的详细信息的屏幕截图。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

撤销删除 blob 的快照后，可单击“提升”将快照复制到根 blob，从而将 blob 还原到快照。

![突出显示 "升级" 选项的 "视图快照" 页的屏幕截图。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要启用软删除，请更新 blob 客户端的服务属性。 下面的示例为订阅中的帐户子集启用了软删除：

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

可以使用以下命令验证是否启用了软删除：

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

若要恢复意外删除的 blob，请在这些 blob 上调用 " **撤消删除 blob** "。 请记住，如果对活动和软删除 blob 调用撤销删除 Blob，则会将所有相关软删除快照还原为活动状态。 下面的示例在容器中的所有软删除 blob 和活动 blob 上调用 **撤消删除 Blob** ：

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
若要查找当前的软删除保留策略，请使用以下命令：

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

若要启用软删除，请更新 blob 客户端的服务属性：

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

若要验证软删除是否已启用，请使用以下命令： 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

若要启用软删除，请更新 blob 客户端的服务属性：

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

若要启用软删除，请更新 blob 客户端的服务属性：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

若要恢复意外删除的 blob，请对这些 blob 调用撤销删除。 请记住，如果对活动和软删除状态的 blob 调用撤销删除，则会将所有相关软删除快照还原为活动状态。 下面的示例对容器中的所有软删除和活动 blob 调用了撤销删除：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

若要恢复到特定 blob 版本，请先对 blob 调用撤销删除，然后将所需快照复制到该 blob。 下面的示例将块 blob 恢复到其最新生成的快照：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

若要启用软删除，请更新 blob 客户端的服务属性：

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

若要恢复意外删除的 blob，请在这些 blob 上调用 " **撤消删除 blob** "。 请记住，如果对活动和软删除 blob 调用撤销删除 Blob，则会将所有相关软删除快照还原为活动状态。 下面的示例在容器中的所有软删除 blob 和活动 blob 上调用 **撤消删除 Blob** ：

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

若要恢复到特定的 blob 版本，请首先调用 " **撤消删除 blob** " 操作，然后将所需的快照复制到 blob 上。 下面的示例将块 blob 恢复到其最新生成的快照：

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>后续步骤

- [Blob 存储的软删除](soft-delete-overview.md)
- [Blob 版本控制（预览版）](versioning-overview.md)
