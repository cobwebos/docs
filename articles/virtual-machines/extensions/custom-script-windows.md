---
title: 适用于 Windows 的 Azure 自定义脚本扩展 | Microsoft 文档
description: 使用自定义脚本扩展自动执行 Windows VM 配置任务
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: roiyz
ms.openlocfilehash: c00d4d481c992e90597276d0ce8655aef2be731f
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038072"
---
# <a name="custom-script-extension-for-windows"></a>适用于 Windows 的自定义脚本扩展

自定义脚本扩展在 Azure 虚拟机上下载并执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时会脚本提供给 Azure 门户。 自定义脚本扩展与 Azure 资源管理器模板集成，也可以使用 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 来运行它。

本文档详细说明如何通过 Azure PowerShell 模块、Azure 资源管理器模板使用自定义脚本扩展，同时详细说明 Windows 系统上的故障排除步骤。

## <a name="prerequisites"></a>先决条件

> [!NOTE]  
> 不要在将同一 VM 作为参数的情况下使用自定义脚本扩展运行 Update-AzureRmVM，因为它将等待它本身响应。  
>   
> 

### <a name="operating-system"></a>操作系统

适用于 Linux 的自定义脚本扩展将在扩展支持的扩展 OS 上运行，有关详细信息，请参阅[此文](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems)。

### <a name="script-location"></a>脚本位置

可使用扩展，利用 Azure Blob 存储凭据来访问 Azure Blob 存储。 或者，脚本位置可以是任何位置，只要 VM 可以路由到该终结点（如 GitHub、内部文件服务器等）即可。


### <a name="internet-connectivity"></a>Internet 连接
如果需要从外部（例如 GitHub 或 Azure 存储）下载脚本，则需要打开其他防火墙/网络安全组端口。 例如，如果脚本位于 Azure 存储中，可以使用[存储](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)的 Azure NSG 服务标记来允许访问。

如果脚本位于本地服务器上，则可能仍需要打开其他防火墙/网络安全组端口。

### <a name="tips-and-tricks"></a>提示和技巧
* 脚本中的语法错误会导致此扩展失败率最高，应测试脚本运行正确无误，同时在脚本中添加其他日志记录，以便更轻松地找到失败位置。
* 编写幂等性的脚本，这样一来，如果脚本意外多次运行，将不会导致系统更改。
* 确保这些脚本在运行时不需要用户输入。
* 脚本可以运行 90 分钟，若运行时间超过 90 分钟，将导致扩展的预配失败。
* 请勿将 reboot 置于脚本中，这会导致正在安装的其他扩展出现问题，并且在重启后，该扩展将不会继续。 
* 如果所拥有的脚本会导致重启，则安装应用程序并运行脚本等。应该使用 Windows 计划任务或者使用 DSC 或 Chef、Puppet 扩展等工具来计划重启。
* 扩展将只运行脚本一次，如果想要在每次启动时运行脚本，则需要使用扩展创建 Windows 计划任务。
* 如果想要计划脚本何时运行，应使用扩展创建 Windows 计划任务。 
* 脚本运行时，Azure 门户或 CLI 中只会显示“正在转换”扩展状态。 如果希望更频繁地更新正在运行的脚本的状态，需要创建自己的解决方案。
* 自定义脚本扩展本身不支持代理服务器，但可以在脚本中使用支持代理服务器的文件传输工具，如 Curl 
* 请注意脚本或命令可能依赖的非默认目录位置，按逻辑对其进行处理。


## <a name="extension-schema"></a>扩展架构

自定义脚本扩展配置指定脚本位置和要运行命令等设置。 可将此配置存储在配置文件中、在命令行中指定，或者在 Azure 资源管理器模板中指定该配置。 

可将敏感数据存储在受保护的配置中，此配置经过加密，只能在虚拟机内部解密。 当执行命令包含机密（例如密码）时，受保护的配置相当有用。

这些项应视为敏感数据，并且应在扩展保护的设置配置中指定。 Azure VM 扩展保护的设置数据已加密，并且只能在目标虚拟机上解密。

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
**注意** - 在某个时间点，只能在 VM 上安装一个扩展版本，在同一资源管理器模板中为同一 VM 指定两次自定义脚本将会失败。 

### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | 日期 |
| 发布者 | Microsoft.Compute | 字符串 |
| type | CustomScriptExtension | 字符串 |
| typeHandlerVersion | 1.9 | int |
| fileUris（例如） | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | 数组 |
| commandToExecute（例如） | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | 字符串 |
| storageAccountName（例如） | examplestorageacct | 字符串 |
| storageAccountKey（例如） | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | 字符串 |

>[!NOTE]
>这些属性名称区分大小写。 要避免部署问题，请使用如下所示的名称。

#### <a name="property-value-details"></a>属性值详细信息
 * `commandToExecute`：（**必需**，字符串）要执行的入口点脚本。 如果命令包含机密（例如密码）或者 fileUris 敏感，请改用此字段。
* `fileUris`：（可选，字符串数组）要下载的文件的 URL。
* `storageAccountName`：（可选，字符串）存储帐户的名称。 如果指定存储凭据，所有 `fileUris` 都必须是 Azure Blob 的 URL。
* `storageAccountKey`：（可选，字符串）存储帐户的访问密钥

可以在公共设置或受保护设置中设置以下值，但扩展会拒绝任何同时在公共设置和受保护设置中设置以下值的配置。
* `commandToExecute`

虽然使用公共设置可能对调试很有用，但强烈建议使用受保护设置。

公共设置会以明文形式发送到将执行脚本的 VM。  受保护设置使用只有 Azure 和 VM 知道的密钥进行加密。 这些设置会在发送时保存到 VM 中，也就是说，如果设置已加密，则会在 VM 上加密保存。 用于对已加密值解密的证书存储在 VM 上，该证书用于在运行时对设置解密（如必要）。

## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行自定义脚本扩展。 可在此处（[GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)）中找到包含自定义脚本扩展的示例模板。

## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 `Set-AzureRmVMCustomScriptExtension` 命令将自定义脚本扩展添加到现有虚拟机。 有关详细信息，请参阅 [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmcustomscriptextension)。
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>更多示例

### <a name="using-multiple-script"></a>使用多个脚本
在此示例中，你有三个用于构建服务器的脚本，“commandToExecute”调用第一个脚本，然后你可以选择如何调用其他脚本，例如，可以使用一个主脚本来控制执行，进行正确的错误处理、日志记录和状态管理。

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzureRmVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>从本地共享运行脚本
在此示例中，你可能希望使用本地 SMB 服务器作为脚本位置，请注意，除  *commandToExecute* 之外，不需要传入任何其他设置。

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzureRmVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>如何使用 CLI 多次运行自定义脚本
如果想要多次运行自定义脚本扩展，则只能在以下条件下执行此操作：
1. 扩展的“Name”参数与之前部署的扩展相同。
2. 必须更新配置，否则命令将不会重新执行，例如，可以将动态属性（例如时间戳）添加到命令中。 

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure PowerShell 模块进行检索。 若要查看给定 VM 的扩展部署状态，请运行以下命令：

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

执行 `commandToExecute` 命令时，扩展会将该目录（例如 `...\Downloads\2`）设置为当前工作目录。 这样可以通过 `fileURIs` 属性使用相对路径查找下载的文件。 请参阅下表中的示例。

绝对下载路径可能会随时间而变化，因此在可能情况下，最好是在 `commandToExecute` 字符串中选择使用相对的脚本/文件路径。 例如：
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

会为通过 `fileUris` 属性列表下载的文件保留第一个 URI 段之后的路径信息。  如下表所示，下载的文件映射到下载子目录中，以便反映 `fileUris` 值的结构。  

#### <a name="examples-of-downloaded-files"></a>下载的文件的示例

| fileUris 中的 URI | 相对下载位置 | 绝对下载位置 * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* 如上所示，绝对目录路径会在 VM 的生存期内更改，但不会在 CustomScript 扩展的某次执行期间更改。

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
