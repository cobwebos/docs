---
title: "适用于 Windows 的 OMS Azure 虚拟机扩展 | Microsoft 文档"
description: "使用虚拟机扩展在 Windows 虚拟机上部署 OMS 代理。"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/08/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 251d7b973426afb50206c428873021144b8bffdf
ms.openlocfilehash: 63e2509b92b4d97bfdc98629cc356816839b03b5


---
# <a name="oms-virtual-machine-extension-for-windows"></a>适用于 Windows 的 OMS 虚拟机扩展

Operations Management Suite (OMS) 提供跨云和本地资产的监视、警报和警报修正功能。 适用于 Windows 的 OMS 代理虚拟机扩展由 Microsoft 发布和提供支持。 该扩展在 Azure 虚拟机上安装 OMS 代理，并将虚拟机注册到现有的 OMS 工作区中。 本文档详细介绍适用于 Windows 的 OMS 虚拟机扩展支持的平台、配置和部署选项。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统
可以在 Windows Server 2008 R2、2012、2012 R2 和 2016 版本中运行适用于 Windows 的 OMS 代理扩展。

### <a name="internet-connectivity"></a>Internet 连接
适用于 Windows 的 OMS 代理扩展要求目标虚拟机已连接到 Internet。 

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 OMS 代理扩展的架构。 此扩展需要目标 OMS 工作区的工作区 ID 和工作区密钥，可以在 OMS 门户中找到此 ID 和密钥。 由于工作区密钥应视为敏感数据，因此它应存储在受保护的设置配置。 Azure VM 扩展的受保护设置数据已加密，并且只能在目标虚拟机上解密。

```json
{
    "type": "extensions",
    "name": "Microsoft.EnterpriseCloud.Monitoring",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

### <a name="property-values"></a>属性值

| 名称 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| 发布者 | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (e.g) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (e.g) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

## <a name="template-deployment"></a>模板部署

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 可以在 Azure Resource Manager 模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure Resource Manager 模板部署过程中运行 OMS 代理扩展。 包含 OMS 代理 VM 扩展的示例模板可以在 [Azure 快速入门库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)中找到。 

## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 `Set-AzureRmVMExtension` 命令将 OMS 代理虚拟机扩展部署到现有的虚拟机。 运行命令之前，需将公共和专用配置存储在 PowerShell 哈希表中。 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS ` 
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure PowerShell 模块进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure PowerShell 模块运行以下命令。

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

扩展执行输出将记录到在以下目录中发现的文件：

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>支持

如果你对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/en-us/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/en-us/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/en-us/support/faq/)。



<!--HONumber=Jan17_HO2-->


