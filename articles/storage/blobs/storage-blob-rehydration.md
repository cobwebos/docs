---
title: 从存档层解除冻结 blob 数据
description: 将 blob 从存档存储中解除冻结，以便可以访问数据。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 1c06c1d0403e526e1ed58a193cfe9b57bb9fe561
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780227"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>从存档层解除冻结 blob 数据

当 blob 位于 "存档" 访问层中时，它被视为 "脱机"，因此无法读取或修改。 Blob 元数据仍处于联机状态并且可用，允许列出 blob 及其属性。 读取和修改 blob 数据仅适用于联机层，如 "热" 或 "冷"。 有两个选项可用于检索和访问存储在存档访问层中的数据。

1. 使用 "[设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)" 操作将[存档的 blob 解除冻结到联机层](#rehydrate-an-archived-blob-to-an-online-tier)，将存档 blob 解除冻结为热或冷。
2. [将已存档的 Blob 复制到联机层](#copy-an-archived-blob-to-an-online-tier)-通过使用 "[复制 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) " 操作创建存档 blob 的新副本。 指定不同的 blob 名称和 "热" 层或 "冷" 层的目标层。

 有关层的详细信息，请参阅[Azure Blob 存储： "热"、"冷" 和 "存档" 访问层](storage-blob-storage-tiers.md)。

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>将存档的 blob 解除冻结到联机层

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>将已存档的 blob 复制到联机层

如果不想解除冻结存档 blob，可以选择执行[复制 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作。 原始 blob 在存档中仍将保持不变，而在联机热层或冷层中创建新的 blob 以便于使用。 在 "复制 Blob" 操作中，你还可以将可选的 "*解除冻结-优先级*" 属性设置为 "标准" 或 "高" （预览）以指定你希望创建 Blob 副本的优先级。

只能将存档 blob 复制到同一存储帐户中的联机目标层。 不支持将存档 blob 复制到另一个存档 blob。

根据所选的解除冻结优先级，从存档复制 blob 可能需要数小时才能完成。 在后台，"**复制 Blob** " 操作将读取存档源 Blob，以便在所选目标层中创建新的联机 Blob。 当你列出 blob 时，新的 blob 可能会可见，但在源存档 blob 完成读取并将数据写入新的联机目标 blob 之前，数据将不可用。 新 blob 作为独立副本，对其进行的任何修改或删除都不会影响源存档 blob。

## <a name="pricing-and-billing"></a>定价和计费

从存档到热层或冷层的解除冻结 blob 按读取操作和数据检索收费。 与标准优先级相比，使用高优先级（预览版）的操作和数据检索成本更高。 高优先级解除冻结在帐单上显示为单独的行项。 如果高优先级请求返回几个千兆字节的存档 blob 需要超过5小时，则不会向你收取高优先级检索速率。 但是，标准检索速率仍适用，因为解除冻结的优先级高于其他请求。

从存档中将 blob 复制到热层或冷层会按读取操作和数据检索收费。 创建新的 blob 副本需要支付写入操作的费用。 当你复制到联机 blob 时，较早的删除费用不适用于，因为源 blob 在存档层中保持不变。 如果选中，将应用高优先级检索费用。

存档层中的 blob 应存储至少180天。 在180天前删除或解除冻结存档的 blob 将导致较早的删除费用。

> [!NOTE]
> 有关块 blob 和数据解除冻结定价的详细信息，请参阅[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。 有关出站数据传输费用的详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="quickstart-scenarios"></a>快速入门方案

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>将存档 blob 解除冻结到联机层
# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)
1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，搜索并选择 "**所有资源**"。

1. 选择存储帐户。

1. 选择容器，然后选择 blob。

1. 在 " **Blob 属性**" 中，选择 "**更改层**"。

1. 选择 "**热**" 或 "**冷**" 访问层。 

1. 选择解除冻结优先级为**标准**或**高**。

1. 选择底部的 "**保存**"。

![更改存储帐户层](media/storage-tiers/blob-access-tier.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
以下 PowerShell 脚本可用于更改存档 blob 的 blob 层。 必须用资源组名称初始化 `$rgName` 变量。 必须用您的存储帐户名称初始化 `$accountName` 变量。 必须用您的容器名称初始化 `$containerName` 变量。 必须用您的 blob 名称初始化 `$blobName` 变量。 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>使用联机层将存档 blob 复制到新的 blob
可以使用以下 PowerShell 脚本将存档 blob 复制到同一存储帐户中的新 blob。 必须用资源组名称初始化 `$rgName` 变量。 必须用您的存储帐户名称初始化 `$accountName` 变量。 必须用您的容器名称初始化 `$srcContainerName` 和 `$destContainerName` 变量。 必须用您的 blob 名称初始化 `$srcBlobName` 和 `$destBlobName` 变量。 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName == ""
$destBlobName == ""

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
