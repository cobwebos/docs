---
title: 将存档层中的 Blob 数据解除冻结
description: 将存档存储中的 Blob 解除冻结，以便你可以访问数据。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 7ec10366ec51be86fa45d25eca87ea86e82338f7
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220445"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>将存档层中的 Blob 数据解除冻结

当 Blob 位于存档访问层时，它被视为处于脱机状态，并且无法读取或修改。 Blob 元数据会保持联机和可用状态，使你可以列出 Blob 及其属性。 读取和修改 Blob 数据仅适用于联机层，如热层或冷层。 有两个选项可用于检索和访问存储在存档访问层中的数据。

1. [将存档的 Blob 解除冻结到联机层](#rehydrate-an-archived-blob-to-an-online-tier) - 通过使用 [设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)操作更改存档 Blob 的层，将存档 Blob 解除冻结到热层或冷层。
2. [将存档的 Blob 复制到联机层](#copy-an-archived-blob-to-an-online-tier) - 通过使用 [复制 Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作创建存档 Blob 的新副本。 指定不同的 Blob 名称以及目标层（热层或冷层）。

 有关层的详细信息，请参阅 [Azure Blob 存储：热访问层、冷访问层和存档访问层](storage-blob-storage-tiers.md)。

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>将已存档的 Blob 解除冻结到联机层

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>将已存档的 blob 复制到联机层

如果不想将存档 Blob 解除冻结，可以选择执行 [复制 Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作。 原始 Blob 将在存档中保持不变，而新的 Blob 将在联机热层或冷层中创建以供你使用。 在“复制 Blob”操作中，还可以将可选的 x-ms-rehydrate-priority 属性置为“标准”或“高”，以指定要在哪个优先级创建你的 Blob 副本。

从存档复制 Blob 可能需要数小时才能完成，具体取决于所选的解除冻结优先级。 在幕后，“复制 Blob”操作将读取存档源 Blob，以便在所选目标层中创建新的联机 Blob。 当你列出 Blob 时，新的 blob 可能会可见，但数据将不可用，直到从源存档 Blob 完成读取并将数据写入到新的联机目标 Blob。 新 Blob 作为独立副本存在，对其进行的任何修改或删除都不会影响源存档 Blob。

> [!IMPORTANT]
> 在目标上成功完成复制之前，请不要删除源 Blob。 如果删除源 Blob，则目标 Blob 可能无法完成复制并且将为空。 可以查看 x-ms-copy-status 来确定复制操作的状态。

存档 Blob 只能复制到同一存储帐户中的联机目标层。 不支持将存档 Blob 复制到另一个存档 Blob。 下表说明了 CopyBlob 的功能。

|                                           | **热层源**   | **冷层源** | **存档层源**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **热层目标**                  | 支持             | 支持            | 在同一帐户中受支持；待解除冻结               |
| **冷层目标**                 | 支持             | 支持            | 在同一帐户中受支持；待解除冻结               |
| **存档层目标**              | 支持             | 支持            | 不支持         |

## <a name="pricing-and-billing"></a>定价和计费

将 Blob 从存档层解除冻结到热层或冷层按读取操作和数据检索收费。 与标准优先级相比，使用高优先级时操作和数据检索的成本更高。 高优先级解除冻结在帐单上显示为单独的行项。 如果满足一个返回若干 GB 的存档 Blob 的高优先级请求需要 5 小时以上的时间，则不会向你收取高优先级检索费用。 但是，标准检索费用仍然适用，因为解除冻结的优先级高于其他请求。

将 Blob 从存档层复制到热层或冷层按读取操作和数据检索收费。 创建新的 Blob 副本按写入操作收费。 当你复制到联机 Blob 时，“提前删除”费用不适用，因为源 Blob 仍然在存档层中保持不变。 如果选择高优先级，则高优先级检索费用将适用。

存档层中的 Blob 应至少存储 180 天。 未满 180 天就将存档的 Blob 删除或解除冻结会产生“提前删除”费用。

> [!NOTE]
> 有关块 Blob 和数据解除冻结的详细定价信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。 有关出站数据传输费用的详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="quickstart-scenarios"></a>快速入门方案

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>将存档 Blob 解除冻结到联机层
# <a name="portal"></a>[门户](#tab/azure-portal)
1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，搜索并选择“所有资源”。

1. 选择存储帐户。

1. 选择容器，然后选择 Blob。

1. 在“Blob 属性”中，选择“更改层”。

1. 选择“热”或“冷”访问层。 

1. 选择“标准”或“高”作为“解除冻结优先级” 。

1. 在底部选择“保存”。

![更改存储帐户层](media/storage-tiers/blob-access-tier.png)
![检查解除冻结状态](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
以下 PowerShell 脚本可用于更改存档 Blob 的 Blob 层。 必须使用你的资源组名称初始化 `$rgName` 变量。 必须使用你的存储帐户名初始化 `$accountName` 变量。 必须使用你的容器名称初始化 `$containerName` 变量。 必须使用你的 Blob 名称初始化 `$blobName` 变量。 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>使用联机层将存档 Blob 复制到新的 Blob
可以使用以下 PowerShell 脚本将存档 Blob 复制到同一存储帐户中的新 Blob。 必须使用你的资源组名称初始化 `$rgName` 变量。 必须使用你的存储帐户名初始化 `$accountName` 变量。 必须使用你的容器名称初始化 `$srcContainerName` 和 `$destContainerName` 变量。 必须使用你的 Blob 名称初始化 `$srcBlobName` 和 `$destBlobName` 变量。 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>后续步骤

* [了解 Blob 存储层](storage-blob-storage-tiers.md)
* [按区域查看 Blob 存储帐户和 GPv2 帐户中的热层、冷层和存档层定价](https://azure.microsoft.com/pricing/details/storage/)
* [管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)
* [检查数据传输定价](https://azure.microsoft.com/pricing/details/data-transfers/)
