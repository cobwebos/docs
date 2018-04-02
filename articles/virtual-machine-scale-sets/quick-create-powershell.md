---
title: 快速入门 - 使用 Azure PowerShell 创建虚拟机规模集 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 快速创建虚拟机规模集
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/18
ms.author: iainfou
ms.openlocfilehash: 8b56502b216246bd0bce9697eb7b401a89173ba8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建虚拟机规模集
利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 可以手动缩放规模集中的 VM 数，也可以定义规则，以便根据资源使用情况（如 CPU 使用率、内存需求或网络流量）进行自动缩放。 然后，Azure 负载均衡器会将流量分配到规模集中的 VM 实例。 在本快速入门中，我们将使用 Azure PowerShell 创建虚拟机规模集并部署一个示例应用程序。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.6.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。


## <a name="create-a-scale-set"></a>创建规模集
使用 [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) 创建虚拟机规模集。 以下示例创建名为 *myScaleSet* 且使用 *Windows Server 2016 Datacenter* 平台映像的规模集。 虚拟网络、公共 IP 地址和负载均衡器的 Azure 网络资源均会自动创建。 出现提示时，请针对规模集中的 VM 实例提供自己的所需管理凭据：

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic"
```

创建和配置所有的规模集资源和 VM 需要几分钟时间。


## <a name="deploy-sample-application"></a>部署示例应用程序
若要测试规模集，请安装一个基本的 Web 应用程序。 使用 Azure 自定义脚本扩展下载并运行一个脚本，以便在 VM 实例上安装 IIS。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 有关详细信息，请参阅[自定义脚本扩展概述](../virtual-machines/windows/extensions-customscript.md)。

使用自定义脚本扩展安装基本的 IIS Web 服务器。 应用可安装 IIS 的自定义脚本扩展，如下所示：

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>测试规模集
若要规模集的运行方式，请在 Web 浏览器中访问示例 Web 应用程序。 使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 获取负载均衡器的公共 IP 地址。 以下示例获取在 *myResourceGroup* 资源组中创建的 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![运行 IIS 网站](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>清理资源
如果不再需要资源组、规模集和所有相关的资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除，如下所示。 `-Force` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。 `-AsJob` 参数会使光标返回提示符处，不会等待操作完成。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>后续步骤
在本快速入门中，我们已创建一个基本的规模集，并使用自定义脚本扩展在 VM 实例上安装了一个基本的 IIS Web 服务器。 若要了解详细信息，请继续学习有关如何创建和管理 Azure 虚拟机规模集的教程。

> [!div class="nextstepaction"]
> [创建和管理 Azure 虚拟机规模集](tutorial-create-and-manage-powershell.md)