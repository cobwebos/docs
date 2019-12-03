---
title: 检查 blob 的加密状态-Azure 存储
description: 了解如何使用 Azure 门户、PowerShell 或 Azure CLI 检查给定的 blob 是否已加密。 如果 blob 未加密，请了解如何使用 AzCopy 通过下载并重新上传 blob 来强制进行加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707597"
---
# <a name="check-the-encryption-status-of-a-blob"></a>检查 blob 的加密状态

2017年10月20日后写入 Azure 存储空间的每个块 blob、追加 blob 或页 blob 都是通过 Azure 存储加密进行加密的。 在此日期之前创建的 blob 继续由后台进程加密。

本文介绍如何确定给定的 blob 是否已加密。

## <a name="check-a-blobs-encryption-status"></a>检查 blob 的加密状态

使用 Azure 门户、PowerShell 或 Azure CLI 来确定是否在不使用代码的情况下加密 blob。

### <a name="azure-portaltabportal"></a>[Azure 门户](#tab/portal)

若要使用 Azure 门户来检查 blob 是否已加密，请执行以下步骤：

1. 在 Azure 门户中，导航到存储帐户。
1. 选择 "**容器**"，导航到帐户中的容器列表。
1. 找到 blob 并显示其 "**概述**" 选项卡。
1. 查看**服务器加密**属性。 如果为**True**（如下图所示），则 blob 已加密。 请注意，blob 的属性还包括创建 blob 的日期和时间。

    ![显示如何在 Azure 门户中检查服务器加密属性的屏幕截图](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 检查 blob 是否已加密，请检查 blob 的**IsServerEncrypted**属性。 请务必将尖括号中的占位符值替换为你自己的值：

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

若要确定创建 blob 的时间，请检查**创建**的属性的值：

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-clitabcli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 来检查 blob 是否已加密，请检查 blob 的**IsServerEncrypted**属性。 请务必将尖括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

若要确定创建 blob 的时间，请检查**创建**的属性的值。

---

## <a name="force-encryption-of-a-blob"></a>强制对 blob 进行加密

如果在2017年10月20日之前创建的 blob 尚未经过后台进程的加密，则可以通过下载并重新上传 blob 来强制进行加密。 实现此目的的简单方法是使用 AzCopy。

若要将 blob 下载到使用 AzCopy 的本地文件系统，请使用以下语法：

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

有关使用 AzCopy 复制 blob 数据的详细信息，请参阅[使用 AzCopy 和 blob 存储传输数据](../common/storage-use-azcopy-blobs.md)。

## <a name="next-steps"></a>后续步骤

[静态数据的 Azure 存储加密](../common/storage-service-encryption.md)
