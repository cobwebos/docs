---
title: "适用于 Windows 的 Azure 自定义脚本扩展 | Microsoft 文档"
description: "使用自定义脚本扩展自动执行 Windows VM 配置任务"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/16/2017
ms.author: danis
ms.openlocfilehash: 25677c2a4ab78c601f4d4a8630787f6f16dda3c4
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="custom-script-extension-for-windows"></a>适用于 Windows 的自定义脚本扩展

自定义脚本扩展在 Azure 虚拟机上下载并执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时会脚本提供给 Azure 门户。 自定义脚本扩展与 Azure 资源管理器模板集成，也可以使用 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 来运行它。

本文档详细说明如何通过 Azure PowerShell 模块、Azure 资源管理器模板使用自定义脚本扩展，同时详细说明 Windows 系统上的故障排除步骤。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

可以针对 Windows 10 客户端、Windows Server 2008 R2、2012、2012 R2 和 2016 版本运行适用于 Windows 的自定义脚本扩展。

### <a name="script-location"></a>脚本位置

脚本需要存储在 Azure Blob 存储中或可通过有效 URL 访问的任何其他位置。

### <a name="internet-connectivity"></a>Internet 连接

适用于 Windows 的自定义脚本扩展要求目标虚拟机已连接到 Internet。 

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示自定义脚本扩展的架构。 该扩展需要脚本位置（Azure 存储或具有有效 URL 的其他位置），以及要执行的命令。 如果使用 Azure 存储作为脚本源，则需要 Azure 存储帐户名称和帐户密钥。 这些项应视为敏感数据，并且应在扩展保护的设置配置中指定。 Azure VM 扩展保护的设置数据已加密，并且只能在目标虚拟机上解密。

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
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
        "typeHandlerVersion": "1.9",
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

| 名称 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| 发布者 | Microsoft.Compute |
| type | 扩展 |
| typeHandlerVersion | 1.9 |
| fileUris（例如） | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute（例如） | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |
| storageAccountName（例如） | examplestorageacct |
| storageAccountKey（例如） | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

请注意 - 这些属性名称区分大小写。 使用上述名称可避免部署问题。

## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行自定义脚本扩展。 可在此处（[GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)）中找到包含自定义脚本扩展的示例模板。

## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 `Set-AzureRmVMCustomScriptExtension` 命令将自定义脚本扩展添加到现有虚拟机。 有关详细信息，请参阅 [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension)。
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

指定的文件将下载到目标虚拟机上的以下目录中。
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
其中，`<n>` 是一个十进制整数，可以在不同的扩展执行之间更改。  `1.*` 值与扩展的 `typeHandlerVersion` 的当前实际值匹配。  例如，实际目录可能是 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`。  

执行 `commandToExecute` 命令时，扩展会将该目录（例如 `...\Downloads\2`）设置为当前的工作目录。 这样可以通过 `fileURIs` 属性使用相对路径查找下载的文件。 请参阅下表中的示例。

绝对下载路径可能会随时间而变化，因此在可能情况下，最好是在 `commandToExecute` 字符串中选择使用相对的脚本/文件路径。 例如：
```json
    "commandToExecute": "powershell.exe . . . -File './scripts/myscript.ps1'"
```

会为通过 `fileUris` 属性列表下载的文件保留第一个 URI 段之后的路径信息。  如下表所示，下载的文件映射到下载子目录中，以便反映 `fileUris` 值的结构。  

#### <a name="examples-of-downloaded-files"></a>下载的文件的示例

| fileUris 中的 URI | 相对下载位置 | 绝对下载位置 * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* 如上所示，绝对目录路径会在 VM 的生存期内更改，但不会在 CustomScript 扩展的某次执行期间更改。

### <a name="support"></a>支持

如果对本文中的任何内容不了解，可以联系 [MSDN Azure 和 Stack Overflow 论坛] (https://azure.microsoft.com/en-us/support/forums/) 上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/en-us/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/en-us/support/faq/)。
