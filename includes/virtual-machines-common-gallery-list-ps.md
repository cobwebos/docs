---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/07/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a55653c0f23be594fe65e7a322c11edc37ee1ce6
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2020
ms.locfileid: "67172912"
---
## <a name="shared-image-management"></a>共享映像管理 

下面是一些常见的管理任务示例，并说明如何使用 PowerShell 完成这些任务。

按名称列出所有库。

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

按名称列出所有映像定义。

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

按名称列出所有映像版本。

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

删除映像版本。 此示例将删除名为“1.0.0”的映像版本  。

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```





