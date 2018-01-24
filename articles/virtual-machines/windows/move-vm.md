---
title: "在 Azure 中移动 Windows VM 资源 | Microsoft Docs"
description: "在 Resource Manager 部署模型中将 Windows VM 移到其他 Azure 订阅或资源组。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: f4b739fd34cc0c85d47b97b7b42a70eb7f5f5ac7
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2017
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>将 Windows VM 移到其他 Azure 订阅或资源组
本文逐步说明如何在资源组或订阅之间移动 Windows VM。 如果最初在个人订阅中创建了 VM，现在想要将其移到公司的订阅以继续工作，则在订阅之间移动 VM 可能很方便。

> [!IMPORTANT]
>不可在此时移动托管磁盘。 
>
>在移动过程中将创建新的资源 ID。 移动 VM 后，需要更新工具和脚本以使用新的资源 ID。 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>使用 PowerShell 移动 VM

要将虚拟机移到其他资源组，需确保同时移动所有依赖资源。 若要使用 Move-AzureRMResource cmdlet，需要各资源的 ResourceId。 可以使用 [Find-AzureRMResource](/powershell/module/azurerm.resources/find-azurermresource) cmdlet 获取 ResourceId 列表。

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

若要移动 VM，需要移动多个资源。 可以使用 Find-AzureRMResource 的输出来创建 ResourceId 的逗号分隔列表并将该列表传递给 [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) 以将这些资源移到目标。 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
要将资源移到其他订阅，请包含 **-DestinationSubscriptionId** 参数的值。 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


系统会要求确认需要移动指定资源。 

## <a name="next-steps"></a>后续步骤
可以在资源组和订阅之间移动许多不同类型的资源。 有关详细信息，请参阅[将资源移到新资源组或订阅](../../resource-group-move-resources.md)。    

