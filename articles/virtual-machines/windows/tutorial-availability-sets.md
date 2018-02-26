---
title: "Azure 中 Windows VM 的可用性集教程 | Microsoft Docs"
description: "了解 Azure 中 Windows VM 的可用性集。"
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 247f86dafe35d69dd742583d246862b739d9fe90
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-use-availability-sets"></a>如何使用可用性集

本教程介绍如何使用称作“可用性集”的功能提高 Azure 上虚拟机解决方案的可用性和可靠性。 可用性集可确保在 Azure 上部署的 VM 能够跨群集中多个隔离的硬件节点分布。 这样，就可以确保当 Azure 中发生硬件或软件故障时，只有一部分 VM 会受到影响，整体解决方案仍可使用和操作。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建可用性集
> * 在可用性集中创建 VM
> * 检查可用的 VM 大小
> * 检查 Azure 顾问

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.3 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。 

## <a name="availability-set-overview"></a>可用性集概述

可用性集是一种逻辑分组功能，在 Azure 中使用它可以确保将 VM 资源部署在 Azure 数据中心后，这些资源相互隔离。 Azure 确保可用性集中部署的 VM 能够跨多个物理服务器、计算机架、存储单元和网络交换机运行。 如果出现硬件或 Azure 软件故障，只有一部分 VM 会受到影响，整体应用程序仍会保持运行，可供客户使用。 如果想要构建可靠的云解决方案，可用性集是一项关键功能。

假设某个基于 VM 的典型解决方案包含四个前端 Web 服务器，以及两个托管数据库的后端 VM。 在 Azure 中，想要在部署 VM 之前先定义两个可用性集：一个可用性集用于 Web 层级，另一个可用性集用于数据库层级。 创建新的 VM 时，可在 az vm create 命令中指定可用性集作为参数，Azure 可自动确保在可用性集中创建的 VM 在多个物理硬件资源之间保持独立。 如果运行某个 Web 服务器或数据库服务器的物理硬件有问题，可以确信 Web 服务器和数据库 VM 的其他实例会保持运行状态，因为它们位于不同的硬件上。

在 Azure 中部署基于 VM 的可靠解决方案时，使用可用性集。

## <a name="create-an-availability-set"></a>创建可用性集

可以使用 [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) 创建一个可用性集。 在本示例中，请将 myResourceGroupAvailability 资源组中名为 myAvailabilitySet 的可用性集的更新域数和容错域数均设置为 2。

创建资源组。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

使用 `-sku aligned` 参数通过 [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) 创建托管的可用性集。

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>在可用性集内创建 VM
必须在可用性集中创建 VM，确保它们正确地分布在硬件中。 创建后，无法将现有 VM 添加到可用性集中。 

同一位置的硬件分为多个更新域和容错域。 更新域是一组可同时重启的 VM 和基础物理硬件。 同一个容错域内的 VM 共享公用存储，以及公用电源和网络交换机。 

通过 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建 VM 时，请使用 `-AvailabilitySetName` 参数指定可用性集的名称。

首先，使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 的管理员用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```

现在，请在可用性集中使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建两个 VM。

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

`-AsJob` 参数以后台任务的方式创建 VM，因此 PowerShell 提示符会返回到你所在的位置。 可以通过 `Job` cmdlet 查看后台作业的详细信息。 创建和配置这两个 VM 需要几分钟的时间完成。 完成后，你将拥有两个跨基础硬件分布的虚拟机。 

如果通过转到“资源组”>“我的资源组可用性”>“我的可用性集”在门户中查看可用性集，则应查看如何跨两个容错域和更新域分布 VM。

![门户中的可用性集](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>检查可用的 VM 大小 

稍后可向可用性集添加更多 VM，但需了解在硬件上可用的 VM 大小。 使用 [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 列出可用性集的硬件群集上所有可用的大小。

```azurepowershell-interactive
Get-AzureRmVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>检查 Azure 顾问 

还可使用 Azure 顾问获取有关提高 VM 可用性的方法的详细信息。 Azure 顾问可帮助遵循最佳做法来优化 Azure 部署。 它可分析资源配置和遥测使用情况，并推荐解决方案，有助于提高 Azure 资源的经济效益、性能、高可用性和安全性。

登录到 [Azure 门户](https://portal.azure.com)，选择“所有服务”，然后键入“顾问”。 顾问仪表板显示针对所选订阅的个性化建议。 有关详细信息，请参阅 [Azure 顾问入门](../../advisor/advisor-get-started.md)。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建可用性集
> * 在可用性集中创建 VM
> * 检查可用的 VM 大小
> * 检查 Azure 顾问

请转到下一教程，了解虚拟机规模集。

> [!div class="nextstepaction"]
> [创建 VM 规模集](tutorial-create-vmss.md)


