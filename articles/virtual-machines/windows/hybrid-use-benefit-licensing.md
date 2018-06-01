---
title: Windows Server 的 Azure 混合权益 | Microsoft Docs
description: 了解如何充分利用 Windows 软件保障权益将本地许可证引入到 Azure 中
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing-ms
ms.openlocfilehash: a4b0baefc8c3c839a06d6540e57b34657138c8ff
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071940"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Windows Server 的 Azure 混合权益
对于有软件保障的客户，Windows Server 的 Azure 混合权益可让你使用本地 Windows Server 许可证，并以较低成本在 Azure 中运行 Windows 虚拟机。 可以使用 Windows Server 的 Azure 混合权益部署 Windows OS 的新虚拟机。 本文介绍如何使用 Windows Server 的 Azure 混合权益部署新的 VM 的步骤，以及如何更新现有正在运行的 VM 的步骤。 有关 Windows Server 的 Azure 混合权益许可和成本节约方面的更多信息，请参阅[“Windows Server 的 Azure 混合权益许可”页](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。

> [!Important]
> 每个双处理器许可证或每组 16 核心许可证均可授权用于多达 8 核心的两个实例，或用于多达 16 核心的一个实例。 Standard Edition 许可证的 Azure 混合权益仅可在本地或 Azure 中使用一次。 Datacenter Edition 权益允许同时在本地和 Azure 中使用。
>

> [!Important]
> 所有区域现在都支持将 Windows Server 的 Azure 混合权益用于运行 Windows Server OS 的任何 VM，其中包括具有其他软件（如 SQL Server 或第三方市场软件）的 VM。 
>

> [!NOTE]
> 对于经典 VM，仅支持从本地自定义映像部署新的 VM。 若要使用本文支持的功能，必须首先将经典 VM 迁移到资源管理器模型。
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>使用 Windows Server 的 Azure 混合权益的方式
可以通过以下几种方式将 Windows 虚拟机与 Azure 混合权限配合使用：

1. 可以从其中一个提供的 [Azure Marketplace 上的 Windows Server 映像](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)部署 VM
2. 用户可以上传自定义 VM，并使用资源管理器模板或 Azure PowerShell 进行部署
3. 你可以在运行 Azure 混合权益或按需支付 Windows Server 成本之间切换和转换现有 VM
4. 还可以在虚拟机规模集上应用 Windows Server 的 Azure 混合权益


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>使用 Windows Server 的 Azure 混合权益创建 VM
Windows Server 的 Azure 混合权益支持所有基于 Windows Server OS 的映像。 可以使用 Azure 平台支持映像或上传自己的自定义 Windows Server 映像。 

### <a name="portal"></a>门户
若要使用 Windows Server 的 Azure 混合权益创建 VM，请使用“节省资金”部分下的切换。

### <a name="powershell"></a>Powershell
```powershell
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>模板
在资源管理器模板中，必须指定附加参数 `licenseType`。 可以阅读有关[创作 Azure 资源管理器模板](../../resource-group-authoring-templates.md)的详细信息
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>将现有 VM 转换为使用 Windows Server 的 Azure 混合权益
如果你要转换现有 VM 以充分利用 Windows Server 的 Azure 混合权益，则可以更新 VM 的许可证类型，如下所示：

### <a name="portal"></a>门户
从门户 VM 边栏选项卡中，可以通过选择"配置"选项将 VM 更新为使用 Azure 混合权益，然后切换“Azure 混合权益”选项

### <a name="powershell"></a>Powershell
- 将现有 Windows Server VM 转换为 Windows Server 的 Azure 混合权益

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
- 将具有权益的 Windows Server VM 转换回即用即付

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- 将现有 Windows Server VM 转换为 Windows Server 的 Azure 混合权益

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```
    
### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>如何验证 VM 是否正在利用许可权益
通过 PowerShell、资源管理器模板或门户部署 VM 后，可使用以下方法验证设置。

### <a name="portal"></a>门户
从门户 VM 边栏选项卡中，选择“配置”选项卡即可查看 Windows Server 的 Azure 混合权益的切换。

### <a name="powershell"></a>Powershell
以下示例显示单个 VM 的许可证类型
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

输出：
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

此输出与下面未使用 Windows Server 的 Azure 混合权益许可部署的 VM 形成鲜明对比：
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query '[?licenseType==Windows_Server]' -o table
```

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>列出订阅中所有具有 Windows Server 的 Azure 混合权益的 VM
若要查看和计算所有使用 Windows Server 的 Azure 混合权益部署的虚拟机，请从订阅运行以下命令：

### <a name="portal"></a>门户
从虚拟机或虚拟机规模集资源边栏选项卡中，可以通过将表列配置为包含“Azure 混合权益”来查看所有 VM 和许可类型的列表。 VM 设置可以处于“已启用”、“未启用”或“不支持”状态。

### <a name="powershell"></a>Powershell
```powershell
$vms = Get-AzureRMVM 
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query '[?licenseType==Windows_Server]' -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>使用 Windows Server 的 Azure 混合权益部署虚拟机规模集
在虚拟机规模集资源管理器模板内，必须在 VirtualMachineProfile 属性中指定额外参数 `licenseType`。 可以通过 ARM 模板、Powershell、Azure CLI 或 REST，在为规模集创建或更新期间执行此操作。

以下示例将 ARM 模板用于 Windows Server 2016 Datacenter 映像：
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
还可以详细了解如何[修改虚拟机规模集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md)，了解更多更新规模集的方法。

## <a name="next-steps"></a>后续步骤
- 详细了解[如何使用 Azure 混合权益节省资金](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- 详细了解 [Azure 混合权益的常见问题解答](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- 详细了解 [Windows Server 的 Azure 混合权益许可详细指南](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)。
- 详细了解 [Windows Server 的 Azure 混合权益和 Azure Site Recovery 让应用迁移到 Azure 更具成本效益](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- 详细了解[如何使用多租户托管权限在 Azure 上部署 Windows 10](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- 详细了解如何[使用资源管理器模板](../../azure-resource-manager/resource-group-overview.md)
