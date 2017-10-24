---
title: "Windows Server 的 Azure 混合权益 | Microsoft Docs"
description: "了解如何充分利用 Windows 软件保障权益将本地许可证引入到 Azure 中"
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/02/2017
ms.author: kmouss
ms.openlocfilehash: d47b8ab2cd6391e937fe7f9ba6eded3b89fe2c40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Windows Server 的 Azure 混合权益
对于有软件保障的客户，Windows Server 的 Azure 混合权益可让你使用本地 Windows Server 许可证，并以较低成本在 Azure 中运行 Windows 虚拟机。 可以使用 Windows Server 的 Azure 混合权益在 Azure 支持的任何平台（Windows Server 映像或 Windows 自定义映像）上部署新虚拟机。 前提是映像不附带其他软件（如 SQL Server 或第三方市场映像）。 本文介绍如何使用 Windows Server 的 Azure 混合权益部署新的 VM 的步骤。 有关 Windows Server 的 Azure 混合权益许可和成本节约方面的更多信息，请参阅[“Windows Server 的 Azure 混合权益许可”页](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。

> [!IMPORTANT]
> 为 Azure Marketplace 上具有企业协议的客户发布的旧版“[HUB]”Windows Server 映像已从 2017 年 9 月 11 日开始停用，请将标准 Windows Server 与门户上 Windows Server 的 Azure 混合权益的“节约资金”选项结合使用。 有关详细信息，请参阅[此文](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions)。
>

> [!NOTE]
> Windows Server 的 Azure 混合权益无法与针对第三方软件（如 SQL Server 或任何第三方市场映像）收费的 VM 一起使用。 遇到 409 错误，类似于以下内容：如果你尝试转换产生其他软件成本的 Windows Server VM，则不允许更改属性“LicenseType”。 
>


> [!NOTE]
> 对于经典 VM，仅支持从本地自定义映像部署新的 VM。 若要使用本文支持的功能，必须首先将经典 VM 迁移到资源管理器模型。
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>使用 Windows Server 的 Azure 混合权益的方式
可以通过以下几种方式将 Windows 虚拟机与 Azure 混合权限配合使用：

1. 可以从其中一个提供的 [Azure Marketplace 上的 Windows Server 映像](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)部署 VM
2. 用户可以[上传自定义 VM](#upload-a-windows-vhd)，并[使用 Resource Manager 模板](#deploy-a-vm-via-resource-manager)或 [Azure PowerShell](#detailed-powershell-deployment-walkthrough) 进行部署
4. 还可以使用 Windows Server 的 Azure 混合权益部署新虚拟机规模集

> [!NOTE]
> 当前不支持转换现有的虚拟机或虚拟机规模集以使用 Windows Server 的 Azure 混合权益
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>从 Windows Server Marketplace 映像部署 VM
使用 Windows Server 的 Azure 混合权益启用所有可从 Azure Marketplace 获取的 Windows Server 映像。 例如，Windows Server 2016、Windows Server 2012 R2、Windows Server 2012 和 Windows Server 2008 SP1 等。 可以使用这些映像直接从 Azure 门户、资源管理器模板、Azure PowerShell 或其他 SDK 部署 VM。

可直接从 Azure 门户中部署这些映像。 对于在 Resource Manager 模板中使用和与 Azure PowerShell 一起使用，请查看如下映像列表：

### <a name="powershell"></a>Powershell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
可以按照相应步骤[使用 PowerShell 创建 Windows 虚拟机](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)并传递 LicenseType = "Windows_Server"。 使用此选项，可以在 Azure 上使用现有的 Windows Server 许可证。

### <a name="portal"></a>门户
可以按照相应步骤[使用 Azure 门户创建 Windows 虚拟机](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)，并选择此选项使用现有的 Windows Server 许可证。

## <a name="upload-a-windows-server-vhd"></a>上传 Windows Server VHD
若要在 Azure 中部署 Windows Server VM，首先需要创建包含基础 Windows 版本的 VHD。 必须先通过 Sysprep 妥善准备此 VHD，再将其上传到 Azure。 可以[阅读有关 VHD 要求和 Sysprep 进程](upload-generalized-managed.md)以及[针对服务器角色的 Sysprep 支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)的详细信息。 在运行 Sysprep 之前备份 VM。 

准备好 VHD 之后，即可将 VHD 上传到 Azure 存储帐户，如下所示：

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server、SharePoint Server 和 Dynamics 还可以利用软件保障许可。 需要准备 Windows Server 映像，方法是安装应用程序组件，并相应地提供许可证密钥，然后将磁盘映像上传到 Azure。 查看使用应用程序运行 Sysprep 的相关文档，例如[使用 Sysprep 安装 SQL Server 的注意事项](https://msdn.microsoft.com/library/ee210754.aspx)或[生成 SharePoint Server 2016 参考映像 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)。
>
>

还可以阅读有关[将 VHD 上传到 Azure 的过程](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)的详细信息


## <a name="deploy-a-vm-via-resource-manager-template"></a>通过 Resource Manager 模板部署 VM
在资源管理器模板中，必须指定附加参数 `licenseType`。 可以阅读有关[创作 Azure Resource Manager 模板](../../resource-group-authoring-templates.md)的详细信息。 将 VHD 上传到 Azure 之后，请编辑 Resource Manager 模板以将许可证类型包含为计算提供程序的一部分，然后照常部署模板：

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>通过 PowerShell 快速入门部署 VM
通过 PowerShell 部署 Windows Server VM 时，可以使用附加参数 `-LicenseType`。 将 VHD 上传到 Azure 之后，可以使用 `New-AzureRmVM` 创建 VM 并指定许可类型，如下所示：

对于 Windows Server：
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

可以阅读更具描述性的指南，了解[使用资源管理器和 PowerShell 创建 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 的不同步骤。


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>验证 VM 是否正在利用许可权益
通过 PowerShell、资源管理器模板或门户部署 VM 后，可使用 `Get-AzureRmVM` 验证许可证类型，如下所示：

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

输出类似于 Windows Server 的以下示例：

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

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>在订阅中列出 Windows Server VM 的所有 Azure 混合权益

若要查看和计算所有使用 Windows Server 的 Azure 混合权益部署的虚拟机，请从订阅运行以下命令：

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>使用 Windows Server 的 Azure 混合权益部署虚拟机规模集
在虚拟机规模集资源管理器模板内，必须指定额外参数 `licenseType`。 可以阅读有关[创作 Azure Resource Manager 模板](../../resource-group-authoring-templates.md)的详细信息。 编辑资源管理器模板以包括 licenseType 属性作为规模集 virtualMachineProfile 的一部分，并以标准方式部署模板 - 请参阅以下使用 2016 Windows Server 映像的示例：


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
还可以[创建和部署虚拟机规模集](#https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create)和设置 LicenseType 属性

## <a name="next-steps"></a>后续步骤
详细了解 [Windows Server 的 Azure 混合权益许可](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。

了解有关[使用 Resource Manager模板](../../azure-resource-manager/resource-group-overview.md)的详细信息。

详细了解 [Windows Server 的 Azure 混合权益和 Azure Site Recovery 使应用程序迁移到 Azure 的过程更经济高效](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)。

详细了解[常见问题](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/)
