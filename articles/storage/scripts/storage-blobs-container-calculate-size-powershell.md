---
title: Azure PowerShell 脚本示例 - 计算 blob 容器大小 | Microsoft Docs
description: 通过计算容器各 blob 的总大小来计算 Azure Blob 存储中容器的大小。
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: d8baec875c25556f1080cdd105c7fa466ffce74e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094001"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>计算 Blob 存储容器的大小

此脚本通过计算容器中 blob 的总大小来计算 Azure Blob 存储中容器的大小。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> 此 PowerShell 脚本提供容器的估计大小，不应该用于计费计算。 有关为计费目的计算容器大小的脚本，请参阅[为计费目的计算 Blob 存储容器的大小](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、容器和所有相关资源。

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令来计算 Blob 存储容器的大小。 表中的每一项均链接到命令特定的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | 获取资源组或订阅中的指定存储帐户或所有存储帐户。 |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | 列出容器中的 Blob。 |

## <a name="next-steps"></a>后续步骤

有关为计费目的计算容器大小的脚本，请参阅[为计费目的计算 Blob 存储容器的大小](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)。

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure 存储的 PowerShell 示例](../blobs/storage-samples-blobs-powershell.md)中找到其他存储 PowerShell 脚本示例。
