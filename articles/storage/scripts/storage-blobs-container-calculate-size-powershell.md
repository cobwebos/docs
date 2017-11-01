---
title: "Azure PowerShell 脚本示例 - 计算 blob 容器大小 | Microsoft Docs"
description: "通过计算容器各 blob 的总大小来计算 Azure Blob 存储中容器的大小。"
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>计算 Blob 存储容器的大小

此脚本通过计算容器中 blob 的总大小来计算 Azure Blob 存储中容器的大小。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>了解 Blob 存储容器的大小

Blob 存储容器的总大小包括容器自身大小，以及所有容器内所有 Blob 的大小。

下面介绍 Blob 容器和 Blob 的存储容量计算方法。 下方的 Len(X) 表示字符串中的字符数。

### <a name="blob-containers"></a>Blob 容器

下面介绍如何估算每个 Blob 容器使用的存储量：

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

以下是明细信息：
* 每个容器的开销（48 字节）包括最后修改时间、权限、公共设置和一些系统元数据。
* 容器名称以 Unicode 形式存储，因此字节数按字符数乘以 2 计算。
* 对于每个存储的 Blob 容器元数据，我们将存储名称长度（以 ASCII 码存储），再加上字符串值的长度。
* 每个签名标识符（512 字节）包括签名标识符名称、开始时间、到期时间和权限。

### <a name="blobs"></a>Blob

下面介绍如何估算每个 Blob 使用的存储量：

* 块 Blob（基本 Blob 或快照）

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* 页 Blob（基本 Blob 或快照）

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

以下是明细信息：

* Blob 的开销（124 字节）包括最后修改时间、大小、缓存控制、内容类型、内容语言、内容编码、内容 MD5、权限、快照信息和一些系统元数据。
* Blob 名称以 Unicode 形式存储，因此字节数按字符数乘以 2 计算。
* 对于每个存储的元数据，则为其名称长度（以 ASCII 码存储），再加上字符串值的长度。
* 对于块 Blob
    * 对块列表为 8 字节
    * 块数乘以块 ID 大小（字节）
    * 加上所有已提交和未提交块中数据的大小。 注意：如果使用了快照，则大小仅包括此基本或快照 Blob 的唯一数据。 如果一周之后未使用未提交的块，这些块将被回收，此时它们将不再计入之后的计费。
* 对于页 Blob
    * 字节数按具有数据的不连续页面范围数乘以 12 计算。 这是在调用 GetPageRanges API 时看到的唯一页面范围数。
    * 加上所有存储页面中的数据大小（字节）。 注意：如果使用了快照，则大小仅包括所清点基本或快照 Blob 的唯一页面。

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>后续步骤

有关 Azure 存储计费的详细信息，请参阅 [Understanding Windows Azure Storage Billing](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)（了解 Windows Azure 存储计费）。

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure 存储的 PowerShell 示例](../blobs/storage-samples-blobs-powershell.md)中找到其他存储 PowerShell 脚本示例。
