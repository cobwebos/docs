---
title: "适用于 Windows 的 Azure 自定义脚本扩展 | Microsoft 文档"
description: "使用自定义脚本扩展自动执行 Windows VM 配置任务"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: f9ea0d14a99a7881b816606058e5ad72a0fef499
ms.openlocfilehash: 01adbd43c5c77c3a80c5141e06a2ab14a49b8454


---
# <a name="custom-script-extension-for-windows"></a>适用于 Windows 的自定义脚本扩展

自定义脚本扩展在 Azure 虚拟机上下载并执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。 自定义脚本扩展与 Azure Resource Manager 模板集成，也可以使用 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 来运行它。

本文档详细说明如何通过 Azure PowerShell 模块、Azure Resource Manager 模板使用自定义脚本扩展，同时详细说明 Windows 系统上的故障排除步骤。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

可以针对 Windows Server 2008 R2、2012、2012 R2 和 2016 版本运行适用于 Windows 的自定义脚本扩展。

### <a name="script-location"></a>脚本位置

脚本需要存储在 Azure 存储中或可通过有效 URL 访问的任何其他位置。

### <a name="internet-connectivity"></a>Internet 连接

适用于 Windows 的自定义脚本扩展要求目标虚拟机已连接到 Internet。 

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示自定义脚本扩展的架构。 该扩展需要脚本位置（Azure 存储或具有有效 URL 的其他位置），以及要执行的命令。 如果使用 Azure 存储作为脚本源，则需要 Azure 存储帐户名称和帐户密钥。 这些项应视为敏感数据，并且应在扩展保护的设置配置中指定。 Azure VM 扩展保护的设置数据已加密，并且只能在目标虚拟机上解密。

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

### <a name="property-values"></a>属性值

| Name | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| 发布者 | Microsoft.Compute |
| type | 扩展 |
| typeHandlerVersion | 1.8 |
| fileUris（例如） | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute（例如） | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |
| storageAccountName（例如） | examplestorageacct |
| storageAccountKey（例如） | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

## <a name="template-deployment"></a>模板部署

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 可以在 Azure Resource Manager 模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure Resource Manager 模板部署过程中运行自定义脚本扩展。 可在此处（[GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)）中找到包含自定义脚本扩展的示例模板。

## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 `Set-AzureRmVMCustomScriptExtension` 命令将自定义脚本扩展添加到现有虚拟机。 有关详细信息，请参阅 [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension)。

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
-VMName myVM `
-Location myLocation `
-FileUri myURL `
-Run 'myScript.ps1' `
-Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure PowerShell 模块进行检索。 若要查看给定 VM 的扩展部署状态，请运行以下命令。

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

扩展执行输出将记录到可在目标虚拟机上的以下目录中找到的文件中。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

脚本本身将下载到目标虚拟机上的以下目录中。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>支持

如果你对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/en-us/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/en-us/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/en-us/support/faq/)。





<!--HONumber=Jan17_HO3-->


