---
title: 教程 - 使用 Azure PowerShell 在规模集中安装应用程序 | Microsoft Docs
description: 了解如何使用自定义脚本扩展通过 Azure PowerShell 将应用程序安装到虚拟机规模集中
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ce35db90e49e0d2861dd71e80ac61f05db607338
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365060"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>教程：使用 Azure PowerShell 在虚拟机规模集中安装应用程序
若要在规模集中的虚拟机 (VM) 实例上运行应用程序，首先需要安装应用程序组件和所需文件。 前一篇教程介绍了如何创建自定义 VM 映像并使用它来部署 VM 实例。 使用此自定义映像可以手动安装和配置应用程序。 也可以在部署每个 VM 实例之后，将应用程序自动安装到规模集，或者更新已在规模集中运行的应用程序。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将应用程序自动安装到规模集
> * 使用 Azure 自定义脚本扩展
> * 更新规模集中运行的应用程序

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 6.0.0 版或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。 


## <a name="what-is-the-azure-custom-script-extension"></a>什么是 Azure 自定义脚本扩展？
自定义脚本扩展在 Azure VM 上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。

自定义脚本扩展与 Azure 资源管理器模板集成，也可以与 Azure CLI 2.0、Azure PowerShell、Azure 门户或 REST API 配合使用。 有关详细信息，请参阅[自定义脚本扩展概述](../virtual-machines/windows/extensions-customscript.md)。

若要查看自定义脚本扩展的运行方式，请创建一个可以安装 IIS Web 服务器并输出规模集 VM 实例主机名的规模集。 自定义脚本扩展定义从 GitHub 下载示例脚本，安装所需的包，然后将 VM 实例主机名写入一个基本的 HTML 页面。


## <a name="create-a-scale-set"></a>创建规模集
现在，使用 [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) 创建虚拟机规模集。 若要将流量分配到单独的 VM 实例，则还要创建负载均衡器。 负载均衡器包含的规则可在 TCP 端口 80 上分配流量，并允许 TCP 端口 3389 上的远程桌面流量，以及 TCP 端口 5985 上的 PowerShell 远程流量。 出现提示时，请针对规模集中的 VM 实例提供自己的所需管理凭据：

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

创建和配置所有的规模集资源和 VM 需要几分钟时间。


## <a name="create-custom-script-extension-definition"></a>创建自定义脚本扩展定义
Azure PowerShell 使用哈希表存储要下载的文件和要执行的命令。 以下示例使用 GitHub 中的示例脚本。 首先，按如下所示创建此配置对象：

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```

接下来，使用 [Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension) 应用自定义脚本扩展。 将前面定义的配置对象传递给扩展。 使用 [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) 在 VM 实例上更新并运行扩展。

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

规模集中的每个 VM 实例将下载并运行 GitHub 中的脚本。 在更复杂的示例中，可以安装多个应用程序组件和文件。 如果规模集已纵向扩展，则新 VM 实例会自动应用相同的自定义脚本扩展定义，并安装所需的应用程序。


## <a name="test-your-scale-set"></a>测试规模集
若要查看运行中的 Web 服务器，请使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 获取负载均衡器的公共 IP 地址。 以下示例获取在 *myResourceGroup* 资源组中创建的 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![IIS 中的基本网页](media/tutorial-install-apps-powershell/running-iis.png)

将 Web 浏览器保持打开，以便在下一步骤中可以看到更新的版本。


## <a name="update-app-deployment"></a>更新应用部署
在规模集的整个生命周期内，都可能需要部署应用程序的更新版本。 使用自定义脚本扩展可以引用更新的部署脚本，然后将扩展重新应用到规模集。 在上一步骤中创建规模集时，`-UpgradePolicyMode` 已设置为 *Automatic*。 此设置可让规模集中的 VM 实例自动更新应用程序并应用其最新版本。

创建名为 *customConfigv2* 的新配置定义。 此定义运行应用程序安装脚本的 *v2* 更新版本：

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

使用 [Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension) 再次将自定义脚本扩展配置应用到规模集中的 VM 实例。 *customConfigv2* 定义用于应用更新版本的应用程序。

```azurepowershell-interactive
# Reapply the Custom Script Extension to install the updated website
$vmss = Add-AzureRmVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $customConfigv2

# Update the scale set and reapply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

规模集中的所有 VM 实例将使用示例网页的最新版本自动更新。 若要查看更新的版本，请在浏览器中刷新网站：

![IIS 中更新的网页](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>清理资源
若要删除规模集和其他资源，请使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组及其所有资源。 `-Force` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。 `-AsJob` 参数会使光标返回提示符处，不会等待操作完成。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>后续步骤
本教程已介绍如何使用 Azure PowerShell 在规模集中自动安装和更新应用程序：

> [!div class="checklist"]
> * 将应用程序自动安装到规模集
> * 使用 Azure 自定义脚本扩展
> * 更新规模集中运行的应用程序

请继续学习下一教程，了解如何自动缩放规模集。

> [!div class="nextstepaction"]
> [自动缩放规模集](tutorial-autoscale-powershell.md)
