---
title: 创建 Azure 映像生成器模板（预览版）
description: 了解如何创建用于 Azure 映像生成器的模板。
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: c13ace67f18b619d5ad86106ecb648db722be9fa
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792439"
---
# <a name="preview-create-an-azure-image-builder-template"></a>预览：创建 Azure 映像生成器模板 

Azure 映像生成器使用 json 文件将信息传递到 Image Builder 服务。 在本文中，我们将深入介绍 json 文件的各个部分，因此你可以构建自己的。 若要查看完整的 json 文件的示例，请参阅[Azure 映像生成器 GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)。

这是基本模板格式：

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>类型和 API 版本

`type`是资源类型，必须是`"Microsoft.VirtualMachineImages/imageTemplates"`。 随着`apiVersion` API 的更改，将随时间而变化，但应`"2019-05-01-preview"`为预览版。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>位置

该位置是要在其中创建自定义映像的区域。 对于图像生成器预览，支持以下区域：

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
默认情况下，映像生成器将使用 "Standard_D1_v2" 生成 VM，你可以重写此设置，例如，如果要为 GPU VM 自定义映像，则需要 GPU VM 大小。 此为可选项。

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

默认情况下，映像生成器不会更改图像的大小，它将使用源图像中的大小。 可以增加操作系统磁盘的大小（Win 和 Linux），这是可选的，值为0表示保留与源映像相同的大小。 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
如果未指定任何 VNET 属性，则映像生成器将创建其自己的 VNET、公共 IP 和 NSG。 公共 IP 用于服务与生成 VM 进行通信，但是，如果不希望使用公共 IP，或者希望映像生成器有权访问现有 VNET 资源（例如配置服务器（DSC、Chef、Puppet、Ansible）、文件共享等），则可以指定 VNET。 有关详细信息，请查看[网络文档](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)，这是可选的。

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Tags

这些是可为生成的映像指定的键/值对。

## <a name="depends-on-optional"></a>取决于（可选）

此可选部分可用于确保在继续操作之前完成依赖项。 

```json
    "dependsOn": [],
```

有关详细信息，请参阅[定义资源依赖项](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)。

## <a name="identity"></a>标识
默认情况下，图像生成器支持使用脚本，或从多个位置（例如 GitHub 和 Azure 存储）复制文件。 若要使用这些功能，必须可公开访问。

你还可以使用由你定义的 Azure 用户分配的托管标识，以允许映像生成器访问 Azure 存储，前提是该标识已在 Azure 存储帐户中至少授予 "存储 Blob 数据读取器"。 这意味着，无需使存储 blob 可供外部访问，也无需安装 SAS 令牌。


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

有关完整示例，请参阅[使用 Azure 用户分配的托管标识访问 Azure 存储中的文件](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)。

图像生成器对用户分配的标识的支持：•仅支持一个标识•不支持自定义域名

若要了解详细信息，请参阅[Azure 资源的托管标识是什么？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
有关部署此功能的详细信息，请参阅[在 AZURE VM 上使用 Azure CLI 配置 azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)。

## <a name="properties-source"></a>属性：源

`source`部分包含有关映像生成器将使用的源映像的信息。

API 需要一个为映像生成定义源的 "SourceType"，当前有三种类型：
- PlatformImage-表示源映像是 Marketplace 映像。
- ManagedImage-从常规托管映像开始时使用此项。
- SharedImageVersion-当使用共享映像库中的映像版本作为源时使用此项。

### <a name="iso-source"></a>ISO 源
我们将从映像生成器弃用此功能，因为现在 RHEL 提供[自己的订阅映像](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos)，请查看以下时间线：
    * 31年3月 2020-资源提供程序现在将不再接受使用 RHEL ISO 源的图像模板。
    * 2020年4月30日-将不再处理包含 RHEL ISO 源的映像模板。

### <a name="platformimage-source"></a>PlatformImage 源 
Azure 映像生成器支持 Windows Server 和客户端以及 Linux Azure Marketplace 映像，请参阅[此处](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support)查看完整列表。 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


此处的属性与使用 AZ CLI 创建 VM 的属性相同，请运行以下内容获取属性： 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

可以在版本中使用 "最新"，在生成映像时对版本进行评估，而不是在提交模板时进行评估。 如果你将此功能与共享映像库目标结合使用，你可以避免重新提交模板，并按时间间隔重新运行映像生成，因此，将从最近的映像重新创建映像。

### <a name="managedimage-source"></a>ManagedImage 源

将源映像设置为通用 VHD 或 VM 的现有托管映像。 源托管映像必须是受支持的操作系统，并且必须与 Azure 映像生成器模板位于同一区域。 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`应为托管映像的 ResourceId。 用于`az image list`列出可用图像。


### <a name="sharedimageversion-source"></a>SharedImageVersion 源
将源映像设置为共享映像库中的现有映像版本。 映像版本必须是受支持的操作系统，并且必须将映像复制到 Azure 映像生成器模板所在的同一区域。 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`应为映像版本的 ResourceId。 使用[az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list)列出映像版本。

## <a name="properties-buildtimeoutinminutes"></a>属性： buildTimeoutInMinutes

默认情况下，映像生成器将运行240分钟。 之后，无论映像生成是否完成，它都将超时和停止。 如果遇到超时，你将看到类似于下面的错误：

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

如果未指定 buildTimeoutInMinutes 值，或将其设置为0，则将使用默认值。 可以增加或减少值，最大值为960mins （16hrs）。 对于 Windows，我们建议不要在下面的60分钟内将其设置为。 如果发现遇到超时，请检查[日志](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)，以查看自定义步骤是否正在等待用户输入之类的内容。 

如果你发现需要更多的时间来完成自定义，请将此项设置为你所需的时间，但会出现一些开销。 但是，不要将它设置得太高，因为可能需要等待它超时，然后才会看到错误。 


## <a name="properties-customize"></a>属性：自定义

图像生成器支持多个 "定制器"。 定制员是用于自定义映像的功能，如运行脚本或重新启动服务器。 

使用`customize`时： 
- 可以使用多个定制员，但必须具有唯一`name`的。
- 定制员按模板中指定的顺序执行。
- 如果一个定制器出现故障，则整个自定义组件将失败并报告回错误。
- 强烈建议您在将脚本用于模板之前对其进行彻底的测试。 在自己的 VM 上调试脚本将会更容易。
- 不要将敏感数据放在脚本中。 
- 除非使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)，否则脚本位置需要可公开访问。

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

 
"自定义" 部分是一个数组。 Azure 映像生成器将按顺序运行定制器。 任何定制员的任何失败都将导致生成过程失败。 
 
 
### <a name="shell-customizer"></a>Shell 定制器

Shell 定制器支持运行 shell 脚本，这些脚本必须可公开访问，IB 才能对其进行访问。

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

操作系统支持： Linux 
 
自定义属性：

- **类型**– Shell 
- **名称**-用于跟踪自定义项的名称 
- **scriptUri** -文件位置的 URI 
- **内联**-shell 命令的数组，用逗号分隔。
- **sha256Checksum** -文件的 sha256 校验和的值，你可以在本地生成它，然后映像生成器将进行校验和验证。
    * 若要使用 Mac/Linux 上的终端生成 sha256Checksum，请运行：`sha256sum <fileName>`


对于以超级用户权限运行的命令，它们必须以为前缀`sudo`。

> [!NOTE]
> 使用 RHEL ISO 源运行 shell 定制器时，需要确保第一个自定义项 shell 在进行任何自定义之前，使用 Red Hat 授权服务器进行注册。 自定义完成后，脚本应向授权服务器取消注册。

### <a name="windows-restart-customizer"></a>Windows 重新启动定制器 
重新启动定制器允许你重新启动 Windows VM 并等待它重新联机，这允许你安装需要重新启动的软件。  

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

OS 支持： Windows
 
自定义属性：
- **类型**： WindowsRestart
- **restartCommand** -执行重新启动的命令（可选）。 默认值为 `'shutdown /r /f /t 0 /c \"packer restart\"'`。
- **restartCheckCommand** –用于检查重新启动是否成功的命令（可选）。 
- **restartTimeout** -以数量级和 unit 的字符串形式指定的重新启动超时。 例如， `5m` （5分钟）或`2h` （2小时）。 默认值为： "5 分钟"

### <a name="linux-restart"></a>Linux 重新启动  
无 Linux 重新启动定制器，但是，如果您要安装驱动程序或需要重新启动的组件，则可以安装这些驱动程序并使用 Shell 定制器调用重新启动，生成 VM 的 SSH 超时为20min。

### <a name="powershell-customizer"></a>PowerShell 定制器 
Shell 定制器支持运行 PowerShell 脚本和内联命令，这些脚本必须可公开访问，IB 才能访问这些脚本。

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

OS 支持： Windows 和 Linux

自定义属性：

- **类型**– PowerShell。
- **scriptUri** -PowerShell 脚本文件所在位置的 URI。 
- **内联**–要运行的内联命令（用逗号分隔）。
- **validExitCodes** –可从 script/inline 命令返回的可选有效代码，这将避免报告脚本/内联命令失败。
- **runElevated** –可选的布尔值，支持通过提升的权限运行命令和脚本。
- **sha256Checksum** -文件的 sha256 校验和的值，你可以在本地生成它，然后映像生成器将进行校验和验证。
    * 若要生成 sha256Checksum，请在 Windows 上使用 PowerShell[获取-哈希](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>文件定制器

文件定制器允许图像生成器从 GitHub 或 Azure 存储下载文件。 如果有依赖于生成项目的映像生成管道，则可以将文件定制器设置为从生成共享下载，并将项目移动到映像。  

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

OS 支持： Linux 和 Windows 

文件定制器属性：

- **sourceUri** -可访问的存储终结点，可以是 GitHub 或 Azure 存储。 您只能下载一个文件，而不是整个目录。 如果需要下载目录，请使用压缩文件，然后使用 Shell 或 PowerShell 定制器对其进行解压缩。 
- **destination** –这是完整的目标路径和文件名。 必须存在任何引用的路径和子目录，使用 Shell 或 PowerShell 定制器预先设置这些路径和子目录。 可以使用脚本定制员创建路径。 

Windows 目录和 Linux 路径支持此操作，但有一些区别： 
- Linux 操作系统–唯一可以写入的路径是/tmp。
- Windows-无路径限制，但路径必须存在。
 
 
如果尝试下载文件时出错，或将其放在指定的目录中，则自定义步骤会失败，这将出现在自定义日志中。

> [!NOTE]
> 文件定制器仅适用于小型文件下载，< 20MB。 对于较大的文件下载，请使用脚本或内联命令，使用代码下载文件，如 Linux `wget`或`curl`、Windows、。 `Invoke-WebRequest`

可以使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)从 Azure 存储下载文件定制器中的文件。

### <a name="windows-update-customizer"></a>Windows 更新定制器
此定制器建立在[社区 Windows 更新配置程序](https://packer.io/docs/provisioners/community-supported.html)for Packer，这是 Packer 社区维护的开源项目。 Microsoft 使用映像生成器服务对配置程序进行测试和验证，并支持调查问题，并解决问题，但 Microsoft 不会正式支持开源项目。 有关 Windows 更新配置程序的详细文档和帮助，请参阅项目存储库。
 
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
OS 支持： Windows

自定义属性：
- **类型**-windowsupdate.log。
- **searchCriteria** -可选，定义要安装的更新类型（建议、重要等），BrowseOnly = 0，IsInstalled = 0 （推荐）为默认值。
- **筛选器**–可选，允许你指定筛选器以包含或排除更新。
- **updateLimit** -可选，定义可安装的更新数，默认值为1000。
 
 

### <a name="generalize"></a>通用化 
默认情况下，Azure 映像生成器还将在每个映像自定义阶段结束时运行 "取消设置" 代码，以 "通用化" 映像。 通用化是设置映像的过程，以便可以重复使用该映像创建多个虚拟机。 对于 Windows Vm，Azure 映像生成器使用 Sysprep。 对于 Linux，Azure 映像生成器运行 "waagent-取消预配"。 

要通用化的命令图像生成器用户可能不适合每种情况，因此，如果需要，Azure 映像生成器将允许您自定义此命令。 

如果要迁移现有自定义，并且使用的是不同的 Sysprep/waagent 命令，则可以使用映像生成器一般命令; 如果 VM 创建失败，请使用自己的 Sysprep 或 waagent 命令。

如果 Azure 映像生成器成功创建了一个 Windows 自定义映像，并且你从该映像创建了一个 VM，然后发现 VM 创建失败或未成功完成，你将需要查看 Windows Server Sysprep 文档或向 Windows Server Sysprep 客户服务支持团队发出支持请求，用户可以对其进行故障排除并建议正确使用 Sysprep。


#### <a name="default-sysprep-command"></a>默认 Sysprep 命令
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>默认 Linux 取消设置命令

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>重写命令
若要重写命令，请使用 PowerShell 或 Shell 脚本设置程序创建具有确切文件名的命令文件，并将其放在正确的目录中：

* Windows： c:\DeprovisioningScript.ps1
* Linux：/tmp/DeprovisioningScript.sh

映像生成器将读取这些命令，这些命令将写出到 AIB 日志 "自定义日志"。 有关如何收集日志的[疑难解答](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs)，请参阅。
 
## <a name="properties-distribute"></a>属性：分布

Azure 映像生成器支持三个分发目标： 

- **managedImage** -托管映像。
- **sharedImage** -共享图像库。
- **Vhd** -存储帐户中的 vhd。

您可以在同一配置中将图像分发到这两个目标类型，请参阅[示例](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)。

由于可以将多个目标分发到，因此，映像生成器会为每个可通过查询来访问的分发目标维护状态`runOutputName`。  `runOutputName`是一个对象，您可以在分发分发中查询该分发的相关信息。 例如，你可以查询 VHD 的位置或复制到的映像版本，或者创建的 SIG 映像版本。 这是每个分布目标的属性。 对于`runOutputName`每个分布目标必须是唯一的。 下面是一个示例，其中查询了共享的映像库分布：

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
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

### <a name="distribute-managedimage"></a>分发： managedImage

图像输出将为托管映像资源。

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
分发属性：
- **类型**– managedImage 
- **imageId** –目标映像的资源 ID，格式应为：/subscriptions/\<subscriptionId>/Resourcegroups/\<destinationResourceGroupName>/providers/microsoft.compute/images/\<imageName>
- **location** -托管映像的位置。  
- **runOutputName** –标识分布的唯一名称。  
- **artifactTags** -可选用户指定的键值对标记。
 
 
> [!NOTE]
> 目标资源组必须存在。
> 如果希望将映像分发到不同的区域，则会增加部署时间。 

### <a name="distribute-sharedimage"></a>分发： sharedImage 
Azure 共享映像库是一种新的映像管理服务，可用于管理映像区域复制、版本控制和共享自定义映像。 Azure 映像生成器支持与此服务进行分发，因此你可以将映像分发到共享映像库支持的区域。 
 
共享映像库由以下内容组成： 
 
- 库-多个共享映像的容器。 库部署在一个区域中。
- 映像定义-图像的概念分组。 
- 映像版本-这是用于部署 VM 或规模集的映像类型。 可以将映像版本复制到需要部署 Vm 的其他区域。
 
在可以分发到映像库之前，必须创建库和映像定义，请参阅[共享图像](shared-images.md)。 

```json
{
    "type": "sharedImage",
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

分布共享图像库的属性：

- **类型**-sharedImage  
- **galleryImageId** –共享映像库的 ID。 格式\<为：/subscriptions/subscriptionId>/resourcegroups/\<resourceGroupName>/Providers/microsoft.compute/galleries/\<sharedImageGalleryName>/images/\<imageGalleryName>。
- **runOutputName** –标识分布的唯一名称。  
- **artifactTags** -可选用户指定的键值对标记。
- **replicationRegions** -用于复制的区域的数组。 其中一个区域必须是部署库的区域。
 
> [!NOTE]
> 可以在库中使用不同区域中的 Azure 映像生成器，但 Azure 映像生成器服务将需要在数据中心之间传输映像，这将需要更长的时间。 图像生成器将基于单调整数自动对图像进行版本，当前无法指定。 

### <a name="distribute-vhd"></a>分发： VHD  
可以输出到 VHD。 然后，你可以复制 VHD，并使用它发布到 Azure MarketPlace，或使用 Azure Stack。  

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
 
OS 支持： Windows 和 Linux

分发 VHD 参数：

- **类型**-VHD。
- **runOutputName** –标识分布的唯一名称。  
- **标记**-可选的用户指定的键值对标记。
 
Azure 映像生成器不允许用户指定存储帐户位置，但可以查询的状态`runOutputs`以获取位置。  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> 创建 VHD 后，请尽快将其复制到其他位置。 VHD 存储在临时资源组中的存储帐户中，该资源组是在将映像模板提交到 Azure 映像生成器服务时创建的。 如果删除了映像模板，则会丢失 VHD。 
 
## <a name="next-steps"></a>后续步骤

[Azure 映像生成器 GitHub](https://github.com/danielsollondon/azvmimagebuilder)中提供了适用于不同方案的示例 json 文件。
 
