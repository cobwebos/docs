---
title: 适用于 Windows 的 Azure 自定义脚本扩展
description: 使用自定义脚本扩展自动执行 Windows VM 配置任务
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 9fe0875f34745b0b5b8b1b7e8b352116b6cbf997
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941910"
---
# <a name="custom-script-extension-for-windows"></a>适用于 Windows 的自定义脚本扩展

自定义脚本扩展在 Azure 虚拟机上下载并执行脚本。 此扩展适用于部署后配置、软件安装或任何其他配置或管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时会脚本提供给 Azure 门户。 自定义脚本扩展与 Azure 资源管理器模板集成，可以使用 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 来运行。

本文档详细说明如何通过 Azure PowerShell 模块、Azure 资源管理器模板使用自定义脚本扩展，同时详细说明 Windows 系统上的故障排除步骤。

## <a name="prerequisites"></a>必备组件

> [!NOTE]  
> 不要在将同一 VM 作为参数的情况下使用自定义脚本扩展运行 Update-AzVM，因为它将等待它本身响应。  

### <a name="operating-system"></a>操作系统

适用于 Windows 的自定义脚本扩展将在扩展支持的扩展操作系统上运行。有关详细信息，请参阅此[支持 Azure 扩展的操作系统](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)。

### <a name="script-location"></a>脚本位置

可以将扩展配置为使用 Azure Blob 存储凭据来访问 Azure Blob 存储。 脚本位置可以是任何位置，只要 VM 可以路由到该终结点，例如 GitHub 或内部文件服务器。

### <a name="internet-connectivity"></a>Internet 连接

如果需要从 GitHub 或 Azure 存储中的外部下载脚本，则需要打开其他防火墙和网络安全组端口。 例如，如果你的脚本位于 Azure 存储中，则可以允许使用 Azure NSG 服务标记进行[存储](../../virtual-network/security-overview.md#service-tags)。

如果你的脚本位于本地服务器上，则你可能仍需要打开额外的防火墙和网络安全组端口。

### <a name="tips-and-tricks"></a>提示和技巧

* 此扩展的最高失败率是由于脚本中的语法错误，测试脚本运行时不会出错，还会将其他日志记录到脚本中，以便更轻松地查找失败的位置。
* 编写具有幂等性的脚本。 这可以确保，如果它们意外再次运行，则不会导致系统更改。
* 确保这些脚本在运行时不需要用户输入。
* 脚本可以运行 90 分钟，若运行时间超过 90 分钟，将导致扩展的预配失败。
* 不要重启置于脚本内，此操作会导致所安装的其他扩展出现问题。 扩展不会在重启之后继续。
* 如果你的脚本将导致重新启动，然后安装应用程序并运行脚本，则可以使用 Windows 计划任务来计划重启，或者使用 DSC、Chef 或 Puppet 扩展等工具。
* 扩展将只运行脚本一次，如果想要在每次启动时运行脚本，则需要使用扩展创建 Windows 计划任务。
* 如果想要计划脚本何时运行，应使用扩展创建 Windows 计划任务。
* 脚本运行时，Azure 门户或 CLI 中只会显示“正在转换”扩展状态。 如果希望更频繁地更新正在运行的脚本的状态，需要创建自己的解决方案。
* 自定义脚本扩展本身不支持代理服务器，但可以在脚本中使用支持代理服务器的文件传输工具，如 Curl
* 请注意脚本或命令可能依赖的非默认目录位置，按逻辑对这种情况进行处理。
* 自定义脚本扩展将在 LocalSystem 帐户下运行

## <a name="extension-schema"></a>扩展架构

自定义脚本扩展配置指定脚本位置和要运行命令等设置。 可将此配置存储在配置文件中、在命令行中指定，或者在 Azure 资源管理器模板中指定该配置。

可将敏感数据存储在受保护的配置中，此配置经过加密，只能在虚拟机内部解密。 当执行命令包含机密（例如密码）时，受保护的配置相当有用。

这些项应视为敏感数据，并且应在扩展保护的设置配置中指定。 Azure VM 扩展保护的设置数据已加密，并且只能在目标虚拟机上解密。

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
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
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> 对 microsoft.managedidentity 属性**不**能与 StorageAccountName 或 storageAccountKey 属性一起使用

> [!NOTE]
> 在某个时间点，只能在 VM 上安装一个扩展版本，在同一 VM 的同一个资源管理器模板中指定自定义脚本两次会失败。

> [!NOTE]
> 可以在 VirtualMachine 资源中使用此架构，也可以将其用作独立资源。 如果在 ARM 模板中将此扩展用作独立资源，则资源名称必须采用以下格式： "virtualMachineName/extensionName"。 

### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| 发布者 | Microsoft.Compute | 字符串 |
| type | CustomScriptExtension | 字符串 |
| typeHandlerVersion | 1.10 | int |
| fileUris（例如） | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp（示例） | 123456789 | 32-bit integer |
| commandToExecute（例如） | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | 字符串 |
| storageAccountName（例如） | examplestorageacct | 字符串 |
| storageAccountKey（例如） | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | 字符串 |
| 对 microsoft.managedidentity （例如） | {} 或 {"clientId"： "31b403aa-c364-4240-a7ff-d85fb6cd7232"} 或 {"objectId"： "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | json 对象 |

>[!NOTE]
>这些属性名称区分大小写。 要避免部署问题，请使用如下所示的名称。

#### <a name="property-value-details"></a>属性值详细信息

* `commandToExecute`：（**必需**，字符串）要执行的入口点脚本。 如果命令包含机密（例如密码）或者 fileUris 敏感，请改用此字段。
* `fileUris`：（可选，字符串数组）要下载的文件的 URL。
* `timestamp`（可选，32 位整数）仅当需要更改此字段的值来触发脚本的重新运行时，才使用此字段。  任何整数值都是可以接受的，前提是必须不同于以前的值。
* `storageAccountName`：（可选，字符串）存储帐户的名称。 如果指定存储凭据，所有 `fileUris` 都必须是 Azure Blob 的 URL。
* `storageAccountKey`：（可选，字符串）存储帐户的访问密钥
* `managedIdentity`：（可选，json 对象）用于下载文件的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
  * `clientId`：（可选，string）托管标识的客户端 ID
  * `objectId`：（可选，string）托管标识的对象 ID

可以在公共设置或受保护设置中设置以下值，但扩展会拒绝任何同时在公共设置和受保护设置中设置以下值的配置。

* `commandToExecute`

使用公共设置可能对调试很有用，但建议使用受保护的设置。

公共设置会以明文形式发送到将执行脚本的 VM。  受保护设置使用只有 Azure 和 VM 知道的密钥进行加密。 这些设置会在发送时保存到 VM，也就是说，如果设置已加密，它们会在 VM 上进行加密保存。 用于对已加密值解密的证书存储在 VM 上，该证书用于在运行时对设置解密（如必要）。

####  <a name="property-managedidentity"></a>属性：对 microsoft.managedidentity

CustomScript （版本1.10.4 向前）支持基于[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的 RBAC，用于从 "fileUris" 设置中提供的 url 下载文件。 它允许 CustomScript 访问 Azure 存储专用 blob/容器，而用户无需传递机密（如 SAS 令牌或存储帐户密钥）。

若要使用此功能，用户必须将[系统分配](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-system-assigned-identity)的或[用户分配](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-user-assigned-identity)的标识添加到要在其中运行 CUSTOMSCRIPT 的 VM 或 VMSS，并向[托管标识授予对 Azure 存储容器或 blob 的访问权限](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access)。

若要在目标 VM/VMSS 上使用系统分配的标识，请将 "对 microsoft.managedidentity" 字段设置为空的 json 对象。 

> 示例：
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

若要在目标 VM/VMSS 上使用用户分配的标识，请将 "对 microsoft.managedidentity" 字段配置为具有托管标识的客户端 ID 或对象 ID。

> 示例：
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> 对 microsoft.managedidentity 属性**不**能与 StorageAccountName 或 storageAccountKey 属性一起使用

## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在部署期间运行自定义脚本扩展。 以下示例显示如何使用自定义脚本扩展：

* [教程：使用 Azure 资源管理器模板部署虚拟机扩展](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [在 Windows 和 Azure SQL DB 上部署双层应用程序](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 `Set-AzVMCustomScriptExtension` 命令将自定义脚本扩展添加到现有虚拟机。 有关详细信息，请参阅 [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension)。

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>其他示例

### <a name="using-multiple-scripts"></a>使用多个脚本

在此示例中，你有三个用于生成服务器的脚本。 **CommandToExecute**调用第一个脚本，您可以选择如何调用其他脚本。 例如，您可以有一个用于控制执行的主脚本，其中包含正确的错误处理、日志记录和状态管理。 脚本将下载到本地计算机以运行。 例如，在 `1_Add_Tools.ps1` 可以通过将 `.\2_Add_Features.ps1` 添加到脚本来调用 `2_Add_Features.ps1`，并为 `$settings`中定义的其他脚本重复此过程。

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>从本地共享运行脚本

在此示例中，可能需要使用本地 SMB 服务器作为脚本位置。 通过执行此操作，无需提供任何其他设置， **commandToExecute**除外。

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>如何使用 CLI 多次运行自定义脚本

如果想要多次运行自定义脚本扩展，则只能在以下条件下执行此操作：

* Extension **Name**参数与扩展的以前部署相同。
* 更新配置，否则不会重新执行该命令。 可以将动态属性添加到命令中，如时间戳。

或者，可以将[ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag)属性设置为**true**。

### <a name="using-invoke-webrequest"></a>使用 WebRequest

如果在脚本中使用[WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) ，则必须指定参数 `-UseBasicParsing` 否则，在检查详细状态时将收到以下错误：

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>经典 VM

若要在经典 Vm 上部署自定义脚本扩展，可以使用 Azure 门户或经典 Azure PowerShell cmdlet。

### <a name="azure-portal"></a>Azure 门户

导航到经典 VM 资源。 选择 "**设置**" 下的 "**扩展**"。

单击 " **+ 添加**"，并在资源列表中选择 "**自定义脚本扩展**"。

在 "**安装扩展**" 页上，选择本地 PowerShell 文件，然后填写所有参数，然后单击 **"确定"** 。

### <a name="powershell"></a>PowerShell

可以使用[AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) Cmdlet 将自定义脚本扩展添加到现有的虚拟机。

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure PowerShell 模块进行检索。 若要查看给定 VM 的扩展部署状态，请运行以下命令：

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

扩展输出记录在目标虚拟机上的以下文件夹中找到的文件中。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定的文件将下载到目标虚拟机上的以下文件夹中。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

其中，`<n>` 是一个十进制整数，可以在不同的扩展执行之间更改。  `1.*` 值与扩展的 `typeHandlerVersion` 的当前实际值匹配。  例如，实际目录可能是 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`。  

执行 `commandToExecute` 命令时，扩展会将该目录（例如 `...\Downloads\2`）设置为当前工作目录。 此过程使得可以通过 `fileURIs` 属性使用相对路径查找下载的文件。 请参阅下表中的示例。

绝对下载路径可能会随时间而变化，因此在可能情况下，最好是在 `commandToExecute` 字符串中选择使用相对的脚本/文件路径。 例如：

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

为通过 `fileUris` 属性列表下载的文件保留第一个 URI 段之后的路径信息。  如下表所示，下载的文件映射到下载子目录中，以便反映 `fileUris` 值的结构。  

#### <a name="examples-of-downloaded-files"></a>下载的文件的示例

| fileUris 中的 URI | 相对下载位置 | 绝对下载位置<sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup>绝对目录路径会在 VM 的生存期内更改，但不会在 CustomScript 扩展的单个执行内。

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 还可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
