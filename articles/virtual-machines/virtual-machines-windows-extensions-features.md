---
title: "适用于 Windows 的虚拟机扩展和功能 | Microsoft 文档"
description: "了解可为 Azure 虚拟机提供哪些扩展，这些虚拟机扩展按它们提供或改进的功能进行分组。"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 1493bf0a545cd605b80404413894bd56e1ced735
ms.openlocfilehash: ebd8734ad2de6f5e528e941f90098de079966f48


---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>适用于 Windows 的虚拟机扩展和功能

Azure 虚拟机扩展是小型应用程序，可在Azure 虚拟机上提供部署后配置和自动化任务。 例如，如果虚拟机要求安装软件、防病毒保护或 Docker 配置，便可以使用 VM 扩展来完成这些任务。 可以使用 Azure CLI、PowerShell、Azure Resource Manager 模板和 Azure 门户运行 Azure VM 扩展。 扩展可与新虚拟机部署捆绑在一起，也可以针对任何现有系统运行。

本文档提供了虚拟机扩展的概述、使用虚拟机扩展的先决条件，以及有关如何检测、管理和删除虚拟机扩展的指南。 本文档提供的是通用信息，因为有许多 VM 扩展可用，每个扩展可能具有独特的配置。 可以在各个扩展特定的各个文档中找到扩展特定的详细信息。

## <a name="use-cases-and-samples"></a>用例和示例

有许多不同的 Azure VM 扩展可用，每个都有特定用例。 一些示例用例包括：

- 使用适用于 Windows 的 DSC 扩展将 PowerShell 所需状态配置应用于虚拟机。 有关详细信息，请参阅 [Azure 所需状态配置扩展](virtual-machines-windows-extensions-dsc-overview.md)。
- 使用 Microsoft 监视代理 VM 扩展配置虚拟机监视。 有关详细信息，请参阅[将 Azure 虚拟机连接到 Log Analytics](../log-analytics/log-analytics-azure-vm-extension.md)。
- 使用 Datadog 扩展配置 Azure 基础结构监视功能。 有关详细信息，请参阅 [Datadog 博客](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)。
- 使用 Chef 配置 Azure 虚拟机。 有关详细信息，请参阅[使用 Chef 自动执行 Azure 虚拟机部署](virtual-machines-windows-chef-automation.md)。

除了进程特定的扩展外，“自定义脚本”扩展也可用于 Windows 和 Linux 虚拟机。 适用于 Windows 的自定义脚本扩展允许在虚拟机上运行任何 PowerShell 脚本。 在设计需要本机 Azure 工具无法提供的配置的 Azure 部署时，这很有用。 有关详细信息，请参阅 [Windows VM 自定义脚本扩展](virtual-machines-windows-extensions-customscript.md)。

若要完成在端到端应用程序部署中使用 VM 扩展的示例，请参阅[将应用程序自动部署到 Azure 虚拟机](virtual-machines-windows-dotnet-core-1-landing.md)。

## <a name="prerequisites"></a>先决条件

每个虚拟机扩展可能都有其自己的一组先决条件。 例如，Docker VM 扩展有支持的 Linux 分发的先决条件。 特定于扩展的文档中详细介绍了各个扩展的要求。

### <a name="azure-vm-agent"></a>Azure VM 代理
Azure VM 代理可管理 Azure 虚拟机与 Azure 结构控制器之间的交互。 VM 代理负责部署和管理 Azure 虚拟机的许多功能层面，包括运行 VM 扩展。 Azure VM 代理预先安装在 Azure 应用商店映像上，并可安装在受支持的操作系统上。

有关受支持的操作系统以及安装说明的信息，请参阅 [Azure virtual machine agent](virtual-machines-windows-agent-user-guide.md)（Azure 虚拟机代理）。

## <a name="discover-vm-extensions"></a>发现 VM 扩展
有许多不同的 VM 扩展可与 Azure 虚拟机配合使用。 若要查看完整列表，请使用 Azure Resource Manager PowerShell 模块运行以下命令。 在运行此命令时，请确保指定所需的位置。

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>运行 VM 扩展

Azure 虚拟机扩展可以在现有虚拟机上运行，当需要在已部署的 VM 上进行配置更改或恢复连接时，这很有用。 VM 扩展还可以与 Azure Resource Manager 模板部署捆绑。 可以将扩展与 Resource Manager 模板配合使用来部署并配置 Azure 虚拟机，在部署后无需干预。

可使用以下方法针对现有虚拟机运行扩展。

### <a name="powershell"></a>PowerShell

存在多个用于运行单个扩展的 PowerShell 命令。 若要查看列表，请运行以下 PowerShell 命令。

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

这将提供类似以下内容的输出：

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

以下示例使用自定义脚本扩展从 GitHub 存储库将脚本下载到目标虚拟机上，然后运行该脚本。 有关 VM 访问扩展的详细信息，请参阅[自定义脚本扩展概述](virtual-machines-windows-extensions-customscript.md)。

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

在此示例中，VM 访问扩展用于重置 Windows 虚拟机的管理密码。 有关 VM 访问扩展的详细信息，请参阅[重置 Windows VM 中的远程桌面服务](virtual-machines-windows-reset-rdp.md)。

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

可以使用 `Set-AzureRmVMExtension` 命令来启动任何 VM 扩展。 有关详细信息，请参阅 [Set-AzureRmVMExtension 参考](https://msdn.microsoft.com/en-us/library/mt603745.aspx)。


### <a name="azure-portal"></a>Azure 门户

可通过 Azure 门户将 VM 扩展应用到现有虚拟机。 为此，请选择要使用的虚拟机，选择“扩展”，然后单击“添加”。 这将提供可用扩展的列表。 选择所需的扩展并按照向导中的步骤进行操作。

下图显示了从 Azure 门户安装 Microsoft 反恶意软件扩展。

![反恶意软件扩展](./media/virtual-machines-windows-extensions-features/anti-virus-extension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager 模板

VM 扩展可添加到 Azure Resource Manager 模板，并在部署模板的过程中执行。 使用模板部署扩展对于创建完全配置的 Azure 部署很有用。 例如，以下 JSON 取自一个 Resource Manager 模板，该模板将在每个 VM 上部署一组负载平衡的虚拟机、一个 Azure SQL 数据库，然后安装一个 .NET Core 应用程序。 VM 扩展负责安装软件。

有关详细信息，请参阅完整的 [Resource Manager 模板](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)。

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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

有关详细信息，请参阅[使用 Windows VM 扩展创作 Azure Resource Manager 模板](virtual-machines-windows-extensions-authoring-templates.md)。

## <a name="secure-vm-extension-data"></a>保护 VM 扩展数据

运行 VM 扩展时，可能需要包括敏感信息，例如凭据、存储帐户名称和存储帐户访问密钥。 许多 VM 扩展包括受保护的配置，该配置对数据进行加密并且仅在目标虚拟机内才对数据进行解密。 每个扩展都有一个特定的受保护的配置架构，扩展特定的文档中详述了各个架构。

以下示例显示了适用于 Windows 的自定义脚本扩展的一个实例。 请注意，要执行的命令包含一组凭据。 在此示例中，不会加密要执行的命令。


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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

将**要执行的命令**属性移动到**受保护的**配置以保护执行字符串。

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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>排查 VM 扩展的问题

每个 VM 扩展可能都有特定的故障排除步骤。 例如，使用自定义脚本扩展时，可在运行该扩展的本地虚拟机上找到脚本执行详细信息。 任何特定于扩展的故障排除步骤均在特定于扩展的文档中详细说明。

以下故障排除步骤适用于所有虚拟机扩展。

### <a name="view-extension-status"></a>查看扩展状态

针对虚拟机运行虚拟机扩展后，使用以下 PowerShell 命令返回扩展状态。 请将示例参数名称替换为你自己的值。 `Name` 参数采用执行时提供给扩展的名称。

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

输出如下所示：

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

此外，还可以在 Azure 门户中找到扩展执行状态。 若要查看扩展的状态，请选择虚拟机，选择“扩展”，然后选择所需的扩展。

### <a name="rerun-vm-extensions"></a>重新运行 VM 扩展

在某些情况下，可能需要重新运行虚拟机扩展。 这可以通过删除扩展，然后使用所选执行方法重新运行扩展来实现。 若要删除扩展，请使用 Azure PowerShell 模块运行以下命令。 请将示例参数名称替换为你自己的值。

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

此外，还可以使用 Azure 门户来删除扩展。 为此，请执行以下操作：

1. 选择一个虚拟机。
2. 选择“扩展”。
3. 选择所需的扩展。
4. 选择“卸载”。

## <a name="common-vm-extensions-reference"></a>常见 VM 扩展参考
| 扩展名称 | 说明 | 详细信息 |
| --- | --- | --- |
| 适用于 Windows 的自定义脚本扩展 |针对 Azure 虚拟机运行脚本 |[适用于 Windows 的自定义脚本扩展](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| 适用于 Windows 的 DSC 扩展 |PowerShell DSC（所需状态配置）扩展 |[适用于 Windows 的 DSC 扩展](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure 诊断扩展 |管理 Azure 诊断 |[Azure 诊断扩展](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM 访问扩展 |管理用户和凭据 |[适用于 Linux 的 VM 访问扩展](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |



<!--HONumber=Feb17_HO1-->


