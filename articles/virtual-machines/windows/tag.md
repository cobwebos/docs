---
title: 如何使用 PowerShell 标记 VM
description: 了解如何使用 PowerShell 标记虚拟机
author: mmccrory
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 3aa40ed396e87b342207fc51576cd28170c7d4e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89322375"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>如何使用 PowerShell 标记 Azure 中的虚拟机
本文介绍在 Azure 中通过 Resource Manager 部署模型标记 Windows 虚拟机的不同方式。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 50 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。 请注意，只有通过 Resource Manager 部署模型创建的资源支持标记。 如果要使用 Azure CLI 标记虚拟机，请参阅 [如何使用 Azure CLI 标记 Azure 中的虚拟机](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>使用 PowerShell 进行标记
要通过 PowerShell 创建、添加和删除标记，首先需要设置[将 PowerShell 环境与 Azure 资源管理器配合使用][PowerShell environment with Azure Resource Manager]。 一旦完成安装后，可以在创建时或创建资源之后通过 PowerShell 将标记放置在计算、网络和存储资源中。 本文章将重点说明查看/编辑虚拟机上放置的标记。

 

首先，通过 `Get-AzVM`cmdlet 导航到虚拟机。

```azurepowershell
PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"
```

如果虚拟机已包含标记，那么会在资源中看到所有标记：

```json
Tags : {
        "Application": "MyApp1",
        "Created By": "MyName",
        "Department": "MyDepartment",
        "Environment": "Production"
        }
```

如果想要通过 PowerShell 添加标记，则可以使用 `Set-AzResource` 命令。 请注意，通过 PowerShell 更新时，标记会作为整体进行更新。 因此，如果要向已具有标记的资源添加标记，则需要包括想要在资源中放置的所有标记。 下面是如何通过 PowerShell Cmdlet 将其他标记添加到资源的示例。

第一个 cmdlet 使用 `Get-AzResource` 和 `Tags` 属性将 *MyTestVM* 中放置的所有标记放置到 *$tags* 变量中。

```azurepowershell
PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags
```

第二个命令显示给定变量的标记。

```azurepowershell
PS C:\> $tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

第三个命令将其他标记添加到 *$tags* 变量。 请注意，使用将 **+=** 新的键/值对追加到 *$tags* 列表。

```azurepowershell
PS C:\> $tags += @{Location="MyLocation"}
```

第四个命令将 *$tags* 变量中定义的标记放置到给定资源中。 在本示例中，该资源为 MyTestVM。

```azurepowershell
PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

第五个命令显示资源上的所有标记。 可以看到，*Location* 现在定义为值为 *MyLocation* 的标记。

```azurepowershell
PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

若要了解有关通过 PowerShell 标记的详细信息，请查看 [Azure 资源 Cmdlet][Azure Resource Cmdlets]。

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述][Azure Resource Manager Overview]和 [使用标记来组织 Azure 资源][Using Tags to organize your Azure Resources]。
* 要了解标记如何帮助你管理 Azure 资源的使用，请参阅 [Understanding your Azure Bill][Understanding your Azure Bill]（了解 Azure 帐单）和 [Gain insights into your Microsoft Azure resource consumption][Gain insights into your Microsoft Azure resource consumption]（深入了解 Microsoft Azure 资源消耗）。

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: /powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
