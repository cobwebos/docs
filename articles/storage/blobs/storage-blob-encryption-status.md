---
title: 检查 blob 的加密状态 - Azure 存储
description: 了解如何使用 Azure 门户、PowerShell 或 Azure CLI 来确定某个给定的 blob 是否已加密。 如果 blob 未加密，请了解如何使用 AzCopy 通过下载和重新上传 blob 来强制执行加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 6d0392cd8e94ba8a9026f557b90e740fbed7f50c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84809092"
---
# <a name="check-the-encryption-status-of-a-blob"></a>检查 blob 的加密状态

2017 年 10 月 20 日后写入 Azure 存储的每个块 Blob、追加 Blob 或页 Blob 均已通过 Azure 存储加密进行了加密。 在此日期之前创建的 Blob 继续由后台进程加密。

本文介绍如何确定某个给定的 blob 是否已加密。

## <a name="check-a-blobs-encryption-status"></a>检查 blob 的加密状态

使用 Azure 门户、PowerShell 或 Azure CLI 来确定某个 blob 是否在不使用代码的情况下进行了加密。

### <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 Azure 门户来检查 blob 是否已加密，请执行以下步骤：

1. 在 Azure 门户中导航到存储帐户。
1. 选择“容器”  ，转到帐户中的容器列表。
1. 找到 blob 并显示其“概述”  选项卡。
1. 查看“加密的服务器”  属性。 如果为“True”  （如下图所示），则表明 blob 已加密。 请注意，blob 的属性还包括 blob 的创建日期和时间。

    ![屏幕截图显示如何在 Azure 门户中查看“加密的服务器”属性](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 检查 blob 是否已加密，请检查 blob 的“IsServerEncrypted”  属性。 请务必将尖括号中的占位符值替换为你自己的值：

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

若要确定 blob 的创建时间，请检查“创建”  属性的值：

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 检查 blob 是否已加密，请检查 blob 的“IsServerEncrypted”  属性。 请务必将尖括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

若要确定 blob 的创建时间，请检查“创建”  属性的值。

---

## <a name="force-encryption-of-a-blob"></a>对 blob 进行强制加密

如果 2017 年 10 月 20 日之前创建的某个 blob 尚未经过后台进程的加密，可以通过下载并重新上传 blob 来即时进行强制加密。 实现此目的的一个简单方法是使用 AzCopy。

若要使用 AzCopy 将 blob 下载到本地文件系统，请使用以下语法：

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

若要使用 AzCopy 将 blob 重新上传到 Azure 存储，请使用以下语法：

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

有关使用 AzCopy 复制 blob 数据的详细信息，请参阅[使用 AzCopy 和 Blob 存储传输数据](../common/storage-use-azcopy-blobs.md)。

## <a name="next-steps"></a>后续步骤

[静态数据的 Azure 存储加密](../common/storage-service-encryption.md)
