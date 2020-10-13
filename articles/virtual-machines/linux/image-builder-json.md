---
title: 创建 Azure 映像生成器模板（预览版）
description: 了解如何创建与 Azure 映像生成器配合使用的模板。
author: danielsollondon
ms.author: danis
ms.date: 08/13/2020
ms.topic: reference
ms.service: virtual-machines
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: 43f33093010aa6a70d02c58e9faa34f7f0e2dfee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91307273"
---
# <a name="preview-create-an-azure-image-builder-template"></a>预览版：创建 Azure 映像生成器模板 

Azure 映像生成器使用一个 .json 文件将信息传入映像生成器服务。 本文将会深入介绍该 json 文件的各个节，使你可以生成自己的模板。 若要查看完整 .json 文件的示例，请参阅 [Azure 映像生成器 GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)。

下面是基本模板格式：

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>类型和 API 版本

`type` 是资源类型，其值必须是 `"Microsoft.VirtualMachineImages/imageTemplates"`。 `apiVersion` 会随着 API 的更改而更改，但对于预览版，其值应是 `"2020-02-14"`。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>位置

位置是要在其中创建自定义映像的区域。 映像生成器预览版支持以下区域：

- 美国东部
- 美国东部 2
- 美国中西部
- 美国西部
- 美国西部 2
- 北欧
- 西欧


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
映像生成器默认使用“Standard_D1_v2”大小的生成 VM，你可以重写此大小。例如，若要自定义 GPU VM 的映像，需使用 GPU VM 大小。 此为可选项。

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

映像生成器默认不会更改映像的大小，它会使用源映像中的大小。 你 **只能** 增加操作系统磁盘的大小 (Win 和 Linux) ，这是可选的，值为0表示保留与源映像相同的大小。 不能将 OS 磁盘大小减少到小于源映像的大小。

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
如果未指定任何 VNET 属性，则映像生成器将创建自身的 VNET、公共 IP 和 NSG。 服务使用公共 IP 与生成 VM 通信。但是，如果不想要使用公共 IP，或者不希望映像生成器能够访问现有的 VNET 资源（例如 DSC、Chef、Puppet 和 Ansible 配置服务器，或者文件共享等），则可以指定 VNET。 有关详细信息，请查看[网络文档](image-builder-networking.md)。此节是可选的。

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Tags

这些是可为生成的映像指定的键/值对。

## <a name="depends-on-optional"></a>Depends on（可选）

可以使用此可选节来确保在继续操作之前完成依赖关系设置。 

```json
    "dependsOn": [],
```

有关详细信息，请参阅[定义资源依赖关系](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)。

## <a name="identity"></a>标识

必需-若要使映像生成器具有读取/写入映像的权限，请从 Azure 存储中读取脚本，你必须创建一个具有单个资源权限的 Azure User-Assigned 标识。 有关映像生成器权限如何工作的详细信息以及相关步骤，请查看 [文档](image-builder-user-assigned-identity.md)。


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


图像生成器支持 User-Assigned 标识：
* 仅支持单个标识
* 不支持自定义域名

有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../../active-directory/managed-identities-azure-resources/overview.md)。
若要详细了解如何部署此功能，请参阅[使用 Azure CLI 在 Azure VM 上配置 Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)。

## <a name="properties-source"></a>属性：source

`source` 节包含有关映像生成器要使用的源映像的信息。 映像生成器目前仅支持创建 (Gen1) 1 映像的 Hyper-v 生成， (SIG) 或托管映像。 如果要创建 Gen2 映像，则需使用源 Gen2 映像，并将其分发到 VHD。 之后，你将需要从 VHD 创建托管映像，并将其作为 Gen2 映像插入到 SIG 中。

API 需要通过一个“SourceType”来定义用于生成映像的源，目前有三种类型：
- PlatformImage - 表示源映像是市场映像。
- ManagedImage - 从常规托管映像开始生成时，请使用此类型。
- SharedImageVersion - 使用共享映像库中的映像版本作为源时，请使用此类型。


> [!NOTE]
> 使用现有的 Windows 自定义映像时，可在单个 Windows 映像上运行 Sysprep 命令最多8次，有关详细信息，请参阅 [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) 文档。

### <a name="platformimage-source"></a>PlatformImage 源 
Azure 映像生成器支持 Windows Server 和客户端以及 Linux Azure 市场映像。有关完整列表，请参阅[此文](../windows/image-builder-overview.md#os-support)。 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


此处的属性与用于创建 VM 的属性相同。请使用 AZ CLI 运行以下命令来获取属性： 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

可以在版本中使用“latest”，版本评估将在映像生成时进行，而不是在提交模板时进行。 如果对共享映像库目标使用此功能，则可以避免重新提交模板，并按时间间隔重新运行映像生成，以便基于最新的映像重新创建映像。

#### <a name="support-for-market-place-plan-information"></a>支持市场位置计划信息
您还可以指定计划信息，例如：
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>ManagedImage 源

将源映像设置为通用化 VHD 或 VM 的现有托管映像。

> [!NOTE]
> 源托管映像必须是受支持的操作系统，并且映像必须与 Azure 映像生成器模板的区域相同。 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` 应是托管映像的 ResourceId。 使用 `az image list` 可以列出可用映像。


### <a name="sharedimageversion-source"></a>SharedImageVersion 源
将源映像设置为共享映像库中的现有映像版本。

> [!NOTE]
> 源托管映像必须是受支持的操作系统，并且映像必须与 Azure 映像生成器模板具有相同的区域，否则请将映像版本复制到映像生成器模板区域。


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` 应是映像版本的 ResourceId。 使用 [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) 可以列出映像版本。


## <a name="properties-buildtimeoutinminutes"></a>属性：buildTimeoutInMinutes

默认情况下，映像生成器将运行 240 分钟。 此后，无论映像生成是否完成，映像生成器都会超时并停止。 如果发生超时，你会看到如下所示的错误：

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

如果未指定 buildTimeoutInMinutes 值，或将其设置为 0，则会使用默认值。 可以增大或减小该值，最大值为 960 分钟（16 小时）。 对于 Windows，我们不建议将此属性设置为 60 分钟以下。 如果发生超时，请查看[日志](image-builder-troubleshoot.md#customization-log)，以确定自定义步骤是否正在等待提供信息（例如用户输入）。 

如果你觉得需要更长时间才能让自定义完成，请将此属性设置为只会略微增大开销的所需值。 但是，不要将它设置得太高，否则可能需要等到它超时才会看到错误。 


## <a name="properties-customize"></a>属性：customize

映像生成器支持多个“定制器”。 定制器是用于自定义映像的功能，例如，运行脚本或重新启动服务器。 

使用 `customize` 时： 
- 可以使用多个定制器，但它们必须具有唯一的 `name`。
- 定制器按模板中指定的顺序执行。
- 如果一个定制器失败，则整个自定义组件将会失败并报告错误。
- 强烈建议在模板中使用脚本之前对其进行全面的测试。 在自己的 VM 上调试脚本会更方便。
- 不要将敏感数据放在脚本中。 
- 脚本位置需可公开访问，除非使用的是 [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)。

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
customize 节是一个数组。 Azure 映像生成器将按顺序运行各个定制器。 任一定制器发生任何失败都会导致生成过程失败。 

> [!NOTE]
> 可在映像模板定义中查看内联命令，并通过 Microsoft 支持部门来帮助支持案例。 如果有敏感信息，则应将其移入 Azure 存储中的脚本，访问需要进行身份验证。
 
### <a name="shell-customizer"></a>Shell 定制器

shell 定制器支持运行 shell 脚本，这些脚本必须可公开访问，这样，IB 才能对其进行访问。

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

OS 支持：Linux 
 
Customize 属性：

- **type** – Shell 
- **name** - 名称，用于跟踪自定义 
- **scriptUri** - 文件位置的 URI 
- **inline** - shell 命令的数组，以逗号分隔。
- **sha256Checksum** - 文件的 sha256 校验和的值。你将在本地生成此校验和，然后，映像生成器会对其进行验证。
    * 若要生成 sha256Checksum，请使用 Mac/Linux 上的终端运行：`sha256sum <fileName>`


要以超级用户特权运行的命令必须带有 `sudo` 前缀。

> [!NOTE]
> 内联命令作为映像模板定义的一部分进行存储，你可以在转储映像定义时看到这些命令，在进行故障排除时，这些命令也可用于 Microsoft 支持部门。 如果有敏感的命令或值，强烈建议将它们移入脚本，并使用用户标识对 Azure 存储进行身份验证。

### <a name="windows-restart-customizer"></a>Windows restart 定制器 
使用 Restart 定制器可以重启 Windows VM 并等待它重新联机，这样，你便可以安装需要重新启动的软件。  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

OS 支持：Windows
 
Customize 属性：
- **类型**：WindowsRestart
- **restartCommand** - 用于执行重启的命令（可选）。 默认为 `'shutdown /r /f /t 0 /c \"packer restart\"'`。
- **restartCheckCommand** - 用于检查重启是否成功的命令（可选）。 
- **restartTimeout** - 以量值和单位的字符串形式指定的重启超时。 例如，`5m`（5 分钟）或 `2h`（2 小时）。 默认值为：“5m”

### <a name="linux-restart"></a>Linux restart  
没有 Linux Restart 定制器，但是，若要安装需要重启的驱动程序或组件，可以安装它们并使用 Shell 定制器调用 restart，在此情况下，生成 VM 的 SSH 超时为 20 分钟。

### <a name="powershell-customizer"></a>PowerShell 定制器 
shell 定制器支持运行 PowerShell 脚本和内联命令，这些脚本必须可公开访问，这样，IB 才能访问这些脚本。

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

OS 支持：Windows 和 Linux

Customize 属性：

- **type** - PowerShell。
- **scriptUri** - PowerShell 脚本文件位置的 URI。 
- **inline** - 要运行的内联命令（以逗号分隔）。
- **validExitCodes** - 可从脚本/内联命令返回的可选有效代码，可避免脚本/内联命令报告失败。
- **runElevated** - 可选布尔值，支持使用提升的权限运行命令和脚本。
- **sha256Checksum** - 文件的 sha256 校验和的值。你将在本地生成此校验和，然后，映像生成器会对其进行验证。
    * 若要生成 sha256Checksum，请使用 Windows 上的 PowerShell [Get-Hash](/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>File 定制器

映像生成器可通过 File 定制器从 GitHub 或 Azure 存储下载文件。 如果你的某个映像生成管道依赖于生成项目，则可将 File 定制器设置为从生成共享进行下载，并将项目移到映像中。  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

OS 支持：Linux 和 Windows 

File 定制器属性：

- **sourceUri** - 可访问的存储终结点，可以是 GitHub 或 Azure 存储。 只能下载一个文件，而不能下载整个目录。 如果需要下载目录，请使用压缩文件，然后使用 Shell 或 PowerShell 定制器将其解压缩。 
- **destination** - 完整的目标路径和文件名。 任何被引用的路径和子目录必须存在，请使用 Shell 或 PowerShell 定制器提前设置这些路径和子目录。 可以使用脚本定制器创建路径。 

Windows 目录和 Linux 路径支持此操作，但存在一些差别： 
- Linux OS - 映像生成器唯一能够写入到的路径是 /tmp。
- Windows - 无路径限制，但路径必须存在。
 
 
如果尝试下载文件时出错或者将文件放在指定的目录中，则自定义步骤将会失败，customization.log 中会记录此状态。

> [!NOTE]
> File 定制器仅适用于下载 < 20MB 的小文件。 要下载较大的文件，请使用脚本、内联命令或代码下载文件，例如，在 Linux 中使用 `wget` 或 `curl`，在 Windows 中使用 `Invoke-WebRequest`。

可以使用 [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage) 从 Azure 存储下载 File 定制器中的文件。

### <a name="windows-update-customizer"></a>Windows Update 定制器
此定制器是基于适用于 Packer 的[社区 Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html)（由 Packer 社区维护的一个开源项目）生成的。 Microsoft 使用映像生成器服务来测试和验证该预配程序，支持使用该服务来调查问题，并会努力解决问题，但该开源项目不受 Microsoft 官方支持。 如需 Windows Update Provisioner 的详细文档和帮助，请参阅项目存储库。

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

Customize 属性：
- **type** - WindowsUpdate。
- **searchCriteria** -（可选）定义安装的更新类型（“建议”、“重要”等），默认设置为 BrowseOnly=0 且 IsInstalled=0（建议）。
- **filters** -（可选）用于指定一个筛选器来包含或排除更新。
- **updateLimit** -（可选）定义可安装的更新数，默认值为 1000。
 
> [!NOTE]
> 如果有任何未完成的 Windows 重启或仍在运行的应用程序安装，Windows 更新定制器可能会失败，通常您可能会在自定义日志中看到此 `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` 错误。 我们强烈建议你在 Windows 重启中添加和/或允许应用程序在运行 Windows 更新之前，使用 [睡眠] 或等待命令 https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/start-sleep?view=powershell-7) 在内联命令或脚本中 (来完成安装。

### <a name="generalize"></a>通用化 
默认情况下，Azure 映像生成器还会在每个映像自定义阶段结束时运行“deprovision”代码，以“通用化”映像。 通用化是设置映像并使其可重复用于创建多个 VM 的过程。 对于 Windows VM，Azure 映像生成器将使用 Sysprep。 对于 Linux，Azure 映像生成器将运行“waagent -deprovision”。 

映像生成器用户要通用化的命令不一定适合每种情况，因此，Azure 映像生成器允许你按需自定义此命令。 

如果你要迁移现有的自定义项，而使用的是不同的 Sysprep/waagent 命令，则可以使用映像生成器泛型命令；如果 VM 创建失败，请使用你自己的 Sysprep 或 waagent 命令。

如果 Azure 映像生成器成功创建了一个 Windows 自定义映像，然后你从该映像创建一个 VM，但发现 VM 创建失败或未成功完成，则在此情况下，需要查看 Windows Server Sysprep 文档或者向 Windows Server Sysprep 客户服务支持团队提出支持请求，他们可以进行故障排除并提供有关 Sysprep 正确用法的建议。


#### <a name="default-sysprep-command"></a>默认的 Sysprep 命令
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>默认的 Linux deprovision 命令

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>重写命令
若要重写命令，请使用 PowerShell 或 Shell 脚本预配程序创建具有确切文件名的命令文件，并将其放到正确的目录中：

* Windows：C:\DeprovisioningScript.ps1
* Linux：/tmp/DeprovisioningScript.sh

映像生成器会读取这些命令，并将其写出到 AIB 日志“customization.log”中。 请参阅[故障排除](image-builder-troubleshoot.md#customization-log)，了解如何收集日志。
 
## <a name="properties-distribute"></a>属性：distribute

Azure 映像生成器支持三个分发目标： 

- **managedImage** - 托管映像。
- **sharedImage** - 共享映像库。
- **VHD** - 存储帐户中的 VHD。

您可以在同一配置中将图像分发到两种目标类型。

> [!NOTE]
> 默认的 AIB sysprep 命令不包括 "/mode： vm"，不过，在创建将安装 HyperV 角色的映像时，这可能是必需的。 如果需要添加此命令参数，则必须重写 sysprep 命令。

由于可以分发到多个目标，映像生成器会维护每个分发目标的状态，可通过查询 `runOutputName` 来访问该状态。  分发后，可以在 `runOutputName` 对象中查询有关该分发的信息。 例如，可以查询 VHD 的位置、映像版本复制到的区域，或者创建的 SIG 映像版本。 这是每个分发目标的属性。 对于每个分发目标，`runOutputName` 必须独一无二。 以下示例查询某个共享映像库分发：

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

输出：
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribute：managedImage

映像输出将是一个托管映像资源。

```json
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
Distribute 属性：
- **type** - managedImage 
- **imageId** –目标映像的资源 ID，格式应为：/Subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **location** - 托管映像的位置。  
- **runOutputName** - 用于标识分发的唯一名称。  
- **artifactTags** -（可选）用户指定的键值对标记。
 
 
> [!NOTE]
> 目标资源组必须存在。
> 将映像分发到不同的区域会增加部署时间。 

### <a name="distribute-sharedimage"></a>Distribute：sharedImage 
Azure 共享映像库是一个新的映像管理服务，可用于管理映像区域复制，以及对自定义映像进行版本控制和共享。 Azure 映像生成器支持使用此服务进行分发，因此你可以将映像分发到共享映像库支持的区域。 
 
共享映像库包括： 
 
- 库 - 多个共享映像的容器。 库部署在一个区域。
- 映像定义 - 映像的概念分组。 
- 映像版本 - 这是用于部署 VM 或规模集的映像类型。 可将映像版本复制到需要部署 VM 的其他区域。
 
必须先创建库和映像定义，然后才能分发到映像库，详见[共享映像](../shared-images-cli.md)。 

```json
{
    "type": "SharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

共享映像库的 Distribute 属性：

- **type** - sharedImage  
- **galleryImageId** –共享映像库的 ID，可采用以下两种格式指定：
    * 自动版本控制-映像生成器将为你生成单调版本号，这对于你想要从同一模板中重新生成映像很有用：格式为： `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` 。
    * 显式版本控制-可以传入希望映像生成器使用的版本号。 格式为： `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** - 用于标识分发的唯一名称。  
- **artifactTags** -（可选）用户指定的键值对标记。
- **replicationRegions** - 用于复制的区域数组。 必须有一个区域是部署库的区域。 添加区域意味着生成时间增加，因为在完成复制之前，生成不会完成。
- **excludeFromLatest** (可选) 这使你可以将所创建的映像版本标记为 SIG 定义中的最新版本，默认值为 "false"。
- **storageAccountType** (可选) AIB 支持为要创建的映像版本指定以下类型的存储：
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> 如果图像模板与所引用的 `image definition` 位置不在同一位置，你将看到创建映像的额外时间。 图像生成器当前没有 `location` 用于映像版本资源的参数，我们从其父代获取参数 `image definition` 。 例如，如果映像定义位于 westus 中，并且你希望将映像版本复制到 eastus，则会将一个 blob 复制到 westus，然后，将在 westus 中创建一个映像版本资源，然后将其复制到 eastus。 若要避免额外的复制时间，请确保 `image definition` 和映像模板位于同一位置。


### <a name="distribute-vhd"></a>Distribute：VHD  
可以输出到 VHD。 然后可以复制该 VHD 并使用它来发布到 Azure 市场，或者将它与 Azure Stack 配合使用。  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
OS 支持：Windows 和 Linux

Distribute VHD 参数：

- **type** - VHD。
- **runOutputName** - 用于标识分发的唯一名称。  
- **tags** -（可选）用户指定的键值对标记。
 
Azure 映像生成器不允许用户指定存储帐户位置，但你可以查询 `runOutputs` 的状态来获取该位置。  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> 创建 VHD 后，请尽快将它复制到其他位置。 VHD 存储在将映像模板提交到 Azure 映像生成器服务时创建的临时资源组中的某个存储帐户内。 如果删除该映像模板，将会丢失 VHD。 

## <a name="image-template-operations"></a>映像模板操作

### <a name="starting-an-image-build"></a>启动映像生成
若要启动生成，需要对映像模板资源调用 "Run"，如命令的示例 `run` ：

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>取消映像生成
如果你运行的是你认为不正确的映像生成，等待用户输入，或者你认为永远不会成功完成，则可以取消生成。

可随时取消生成。 如果分发阶段已开始，你仍可以取消，但你将需要清除可能未完成的任何映像。 "取消" 命令不等待 "取消" 完成，请 `lastrunstatus.runstate` 使用这些状态 [命令](image-builder-troubleshoot.md#customization-log)监视取消进度。


命令示例 `cancel` ：

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>后续步骤

[Azure 映像生成器 GitHub](https://github.com/danielsollondon/azvmimagebuilder) 中提供了适用于不同方案的示例 .json 文件。
