---
title: 从存档层解冻 Blob 数据
description: 从存档存储中解冻 Blob，以便可以访问数据。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: cbaa1d34eb8fe44b1c367d8fa3f84687fe7568e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84433179"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>从存档层解冻 Blob 数据

当 Blob 位于存档访问层时，它被视为脱机状态，无法对其进行读取或修改。 Blob 元数据保持联机和可用状态，你可以列出 Blob 及其属性。 只能读取和修改联机层（例如热层或冷层）中的 Blob 数据。 可使用两个选项来检索和访问存档访问层中存储的数据。

1. [将存档的 Blob 解冻到联机层](#rehydrate-an-archived-blob-to-an-online-tier) - 使用[设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)操作将存档的 Blob 解冻至热层或冷层。
2. [将存档的 Blob 复制到联机层](#copy-an-archived-blob-to-an-online-tier) - 使用[复制 Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 操作创建已存档 Blob 的新副本。 指定不同的 Blob 名称，以及目标热层或冷层。

 有关层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](storage-blob-storage-tiers.md)。

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>将存档的 Blob 解冻到联机层

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>将存档的 Blob 复制到联机层

如果你不想要解冻存档的 Blob，可以选择执行[复制 Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 操作。 原始 Blob 在存档中保持未修改状态，同时会在热层或冷层中联机创建新的 Blob 供你使用。 在“复制 Blob”操作中，还可以将可选的 x-ms-rehydrate-priority 属性置为“标准”或“高”，以指定要在哪个优先级创建你的 Blob 副本。

从存档中复制 Blob 可能需要数小时才能完成，具体取决于所选解冻优先级。 在幕后，“复制 Blob”操作会读取存档源 Blob，以便在所选目标层中创建新的联机 Blob。 列出 Blob 时，新 Blob 也许可见，但数据并不可用，直到从源存档 Blob 进行读取的操作完成并将数据写入到新的联机目标 Blob 为止。 新 Blob 充当独立的副本，对它进行的任何修改或删除操作不会影响源存档 Blob。

> [!IMPORTANT]
> 在目标上成功完成复制之前，请不要删除源 Blob。 如果删除源 Blob，则目标 Blob 可能无法完成复制并且将为空。 可以查看 x-ms-copy-status 来确定复制操作的状态。

只能将存档 Blob 复制到同一存储帐户中的联机目标层。 不支持将存档 Blob 复制到另一个存档 Blob。 下表说明了 CopyBlob 的功能。

|                                           | **热层源**   | **冷层源** | **存档层源**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **热层目标**                  | 支持             | 支持            | 在同一帐户中受支持；待解除冻结               |
| **冷层目标**                 | 支持             | 支持            | 在同一帐户中受支持；待解除冻结               |
| **存档层目标**              | 支持             | 支持            | 不支持         |

## <a name="pricing-and-billing"></a>定价和计费

将存档中的 Blob 解冻到热层或冷层会产生读取操作和数据检索费用。 与标准优先级相比，使用高优先级时操作和数据检索的成本更高。 高优先级解冻在账单上单独显示费用细目。 如果返回若干 GB 存档 Blob 的高优先级请求花费了 5 小时以上，则不会按照高优先级检索费率向你收费。 不过，标准检索费率仍适用，因为解冻的优先级高于其他请求。

将存档中的 Blob 复制到热层或冷层会产生读取操作和数据检索费用。 创建新的 Blob 副本会产生写入操作费用。 复制到联机 Blob 时，不会产生提前删除费，因为源 Blob 在存档层中保持未修改状态。 高优先级检索费用适用（如果已选中）。

存档层中的 Blob 应至少存储 180 天。 在 180 天之前删除或解冻存档的 Blob 会导致提前删除费。

> [!NOTE]
> 有关块 Blob 和数据解冻的定价详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。 有关出站数据传输费的详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="quickstart-scenarios"></a>快速入门方案

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>将存档 Blob 解冻到联机层
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，搜索并选择“所有资源”。

1. 选择存储帐户。

1. 选择容器，然后选择自己的 blob。

1. 在“Blob 属性”中选择“更改层”。 

1. 选择“热”或“冷”访问层。  

1. 选择“标准”或“高”作为“解除冻结优先级”。 

1. 选择底部的“保存”。

![更改存储帐户层](media/storage-tiers/blob-access-tier.png)
![检查解除冻结状态](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
以下 PowerShell 脚本可用于更改存档 blob 的 blob 层。 必须使用资源组名称初始化 `$rgName` 变量。 必须使用存储帐户名称初始化 `$accountName` 变量。 必须使用容器名称初始化 `$containerName` 变量。 必须使用 Blob 名称初始化 `$blobName` 变量。 
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
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>将存档 Blob 复制到联机层的新 Blob
可以使用以下 PowerShell 脚本将存档 Blob 复制到同一存储帐户中的新 Blob。 必须使用资源组名称初始化 `$rgName` 变量。 必须使用存储帐户名称初始化 `$accountName` 变量。 必须使用容器名称初始化 `$srcContainerName` 和 `$destContainerName` 变量。 必须使用 Blob 名称初始化 `$srcBlobName` 和 `$destBlobName` 变量。 
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
