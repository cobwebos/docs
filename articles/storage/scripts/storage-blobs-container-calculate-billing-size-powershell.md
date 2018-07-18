---
title: Azure PowerShell 脚本示例 - 计算要计费的 Blob 容器总大小 | Microsoft Docs
description: 出于计费目的计算 Azure Blob 存储中容器的总大小。
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: c37b416578a76e9b12e29d68e413d851796ccc6f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
ms.locfileid: "26368538"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>计算要计费的 Blob 容器总大小

此脚本出于估算计费成本的目的，计算 Azure Blob 存储中的容器大小。 此脚本计算容器中各 blob 的大小总和。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> 此 PowerShell 脚本出于计费目的计算容器大小。 如果要出于其他目的计算容器大小，请参阅[计算 Blob 存储容器的总大小](../scripts/storage-blobs-container-calculate-size-powershell.md)，获取进行估算的更简单的脚本。

## <a name="determine-the-size-of-the-blob-container"></a>确定 Blob 容器的大小

Blob 容器的总大小包括容器自身大小，以及容器内所有 blob 的大小。

下述部分介绍 Blob 容器和 blob 的存储容量计算方法。 在下一部分中，Len(X) 表示字符串中的字符数。

### <a name="blob-containers"></a>Blob 容器

下述计算介绍如何估算每个 Blob 容器使用的存储量：

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

以下是明细信息：
* 每个容器的开销（48 字节）包括最后修改时间、权限、公共设置和一些系统元数据。

* 容器名称以 Unicode 形式存储，因此字节数按字符数乘以 2 计算。

* 对于存储的每个 Blob 容器元数据块，我们将存储名称 (ASCII) 长度，再加上字符串值的长度。

* 每个签名标识符（512 字节）包括签名标识符名称、开始时间、到期时间和权限。

### <a name="blobs"></a>Blob

下述计算显示如何估算每个 blob 使用的存储量。

* 块 blob（基本 blob 或快照）：

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* 页 blob（基本 blob 或快照）：

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

以下是明细信息：

* Blob 的开销为 124 字节，其中包括：
    - 上次修改时间
    - 大小
    - Cache-Control
    - Content-Type
    - Content-Language
    - Content-Encoding
    - Content-MD5
    - 权限
    - 快照信息
    - Lease
    - 某些系统元数据

* Blob 名称以 Unicode 形式存储，因此字节数按字符数乘以 2 计算。

* 对于每个存储的元数据块，添加名称长度（以 ASCII 码存储），再加上字符串值的长度。

* 对于块 blob：
    * 块列表为 8 字节。
    * 块数乘以块 ID 大小（按字节计）。
    * 所有已提交和未提交块中数据的大小。 
    
    >[!NOTE]
    >使用快照时，大小仅包括此基本或快照 blob 的唯一数据。 如果未提交块在一周后未被使用，则回收到垃圾桶。 之后不计入账单。

* 对于页 blob：
    * 字节数按具有数据的不连续页面范围数乘以 12 计算。 这是在调用 GetPageRanges API 时看到的唯一页面范围数。

    * 所有存储页面中的数据大小（按字节计）。 
    
    >[!NOTE]
    >使用快照时，大小仅包含要计数的基本 blob 或快照 blob 的唯一页面。

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>后续步骤

- 请参阅[计算 Blob 存储容器的总大小](../scripts/storage-blobs-container-calculate-size-powershell.md)，获取估算容器大小的简单脚本。

- 有关 Azure 存储计费的详细信息，请参阅[了解 Windows Azure 存储计费](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)。

- 有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.1)。

- 有关其他存储 PowerShell 脚本示例，可查看[适用于 Azure 存储的 PowerShell 示例](../blobs/storage-samples-blobs-powershell.md)。
