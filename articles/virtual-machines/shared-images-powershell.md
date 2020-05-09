---
title: 使用 Azure PowerShell 创建共享映像库
description: 了解如何使用 Azure PowerShell 在 Azure 中创建共享映像库
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: b7625903c4640fdb17eacd29822fe6203e075826
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796988"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>使用 Azure PowerShell 创建共享映像库 

[共享映像库](./windows/shared-image-galleries.md)大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动部署任务，例如预加载应用程序、应用程序配置和其他 OS 配置。 

使用共享映像库，你可以在 AAD 租户内在同一区域或跨区域与组织中的其他用户共享自定义 VM 映像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 你可以创建多个库，以便可以按逻辑方式对共享映像进行分组。 

库是顶级资源，它提供完全基于角色的访问控制 (RBAC)。 你可以控制映像的版本，并且可以选择将每个映像版本复制到一组不同的 Azure 区域。 库仅适用于托管映像。

共享映像库功能具有多种资源类型。 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


[!INCLUDE [virtual-machines-common-shared-images-powershell](../../includes/virtual-machines-common-shared-images-powershell.md)]


## <a name="next-steps"></a>后续步骤

从[VM](image-version-vm-powershell.md)、[托管映像](image-version-managed-image-powershell.md)或[其他库中的映像](image-version-another-gallery-powershell.md)创建映像。

[Azure 映像生成器（预览版）](./windows/image-builder-overview.md)可帮助自动创建映像版本，甚至还可以使用它来更新[现有映像版本并创建新的映像版本](./windows/image-builder-gallery-update-image-version.md)。 

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共享映像库中创建映像定义](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [从映像版本创建 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


