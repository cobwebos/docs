---
title: 创建 Azure 映像生成器模板（预览）
description: 了解如何创建要与 Azure 映像生成器一起使用的模板。
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246783"
---
# <a name="preview-create-an-azure-image-builder-template"></a>预览：创建 Azure 映像生成器模板 

Azure 映像生成器使用 .json 文件将信息传递到映像生成器服务。 在本文中，我们将介绍 json 文件的各个部分，以便您可以构建自己的文件。 要查看完整的 .json 文件的示例，请参阅[Azure 映像生成器 GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)。

这是基本的模板格式：

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

`type`是 资源类型，必须为`"Microsoft.VirtualMachineImages/imageTemplates"`。 随着`apiVersion`API 的变化，将随时间而变化，但应为`"2019-05-01-preview"`预览。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>位置

位置是将创建自定义映像的区域。 对于图像生成器预览，支持以下区域：

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
默认情况下，映像生成器将使用"Standard_D1_v2"生成 VM，您可以重写这一点，例如，如果要为 GPU VM 自定义映像，则需要 GPU VM 大小。 此为可选项。

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

默认情况下，图像生成器不会更改图像的大小，它将使用源图像中的大小。 您可以增加 OS 磁盘（Win 和 Linux）的大小，这是可选的，值为 0 表示保留与源映像相同的大小。 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
如果不指定任何 VNET 属性，则映像生成器将创建自己的 VNET、公共 IP 和 NSG。 公共 IP 用于服务与生成 VM 通信，但是，如果您不希望公共 IP 或希望映像生成器有权访问现有的 VNET 资源，例如配置服务器（DSC、Chef、Puppet、Ansible）、文件共享等。，然后您可以指定 VNET。 有关详细信息，请查看[网络文档](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)，这是可选的。

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Tags

这些是键/值对，您可以为生成的图像指定。

## <a name="depends-on-optional"></a>取决于（可选）

此可选部分可用于确保依赖项在继续之前完成。 

```json
    "dependsOn": [],
```

有关详细信息，请参阅[定义资源依赖项](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)。

## <a name="identity"></a>标识
默认情况下，映像生成器支持使用脚本或从多个位置（如 GitHub 和 Azure 存储）复制文件。 要使用这些，它们必须是可公开访问的。

您还可以使用由您定义的 Azure 用户分配的托管标识，允许映像生成器访问 Azure 存储，只要该标识已被授予 Azure 存储帐户上的最低"存储 Blob 数据读取器"。 这意味着您不需要使存储 blob 在外部访问，或设置 SAS 令牌。


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

有关完整示例，请参阅[使用 Azure 用户分配的托管标识访问 Azure 存储 中的文件](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)。

图像生成器对用户分配标识的支持：• 仅支持单个标识 • 不支持自定义域名

要了解更多信息，请参阅[Azure 资源的托管标识是什么？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
有关部署此功能的详细信息，请参阅使用 Azure [CLI 在 Azure VM 上配置 Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)。

## <a name="properties-source"></a>属性：源

该`source`部分包含有关图像生成器将使用的源映像的信息。

API 需要一个"SourceType"来定义映像生成源，目前有三种类型：
- PlatformImage - 指示源映像是应用商店映像。
- 托管映像 - 从常规托管映像开始时，请使用此选项。
- 共享图像版本 - 当您在共享图像库中使用图像版本作为源时，将使用此版本。

### <a name="iso-source"></a>ISO 源
我们正在从映像生成器中弃用此功能，因为现在有[RHEL 自带订阅映像](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos)，请查看下面的时间线：
    * 2020 年 3 月 31 日 - 资源提供商现在将接受具有 RHEL ISO 源的图像模板。
    * 2020 年 4 月 30 日 - 包含 RHEL ISO 源的图像模板将不再处理。

### <a name="platformimage-source"></a>平台图像源 
Azure 映像生成器支持 Windows 服务器和客户端以及 Linux Azure 应用商店映像，请参阅[此处](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support)查看完整列表。 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


此处的属性与使用 AZ CLI 创建 VM 的属性相同，请运行以下属性以获取属性： 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

您可以在版本中使用"最新"，在进行映像生成时评估版本，而不是提交模板时。 如果将此功能与共享图像库目标一起使用，则可以避免重新提交模板，并每隔一段时间重新运行图像生成，以便从最新图像重新创建图像。

### <a name="managedimage-source"></a>托管图像源

将源映像集为通用 VHD 或 VM 的现有托管映像。 源托管映像必须为受支持的操作系统，并且与 Azure 映像生成器模板位于同一区域。 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`应为托管映像的 ResourceID。 用于`az image list`列出可用图像。


### <a name="sharedimageversion-source"></a>共享图像版本源
在共享映像库中设置现有映像版本的源映像。 映像版本必须为受支持的操作系统，并且映像必须复制到与 Azure 映像生成器模板相同的区域。 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`应为映像版本的 ResourceId。 使用[az sig 图像版本列表](/cli/azure/sig/image-version#az-sig-image-version-list)列出图像版本。

## <a name="properties-buildtimeoutinminutes"></a>属性：生成时间分钟

默认情况下，映像生成器将运行 240 分钟。 之后，无论映像生成是否完成，它将超时并停止。 如果达到超时，您将看到类似于此的错误：

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

如果不指定 buildTimeoutIn分钟值，或将其设置为 0，则将使用默认值。 您可以增加或减小该值，最高可达 960 分钟（16 小时）。 对于 Windows，我们不建议将此设置在 60 分钟以下。 如果您发现您正在达到超时，请查看[日志](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)，以查看自定义步骤是否正在等待用户输入之类的内容。 

如果您发现需要更多时间完成自定义，则设置到您认为需要的内容，并稍加开销。 但是，不要设置过高，因为您可能需要等待超时，才能看到错误。 


## <a name="properties-customize"></a>属性：自定义

图像生成器支持多个"自定义器"。 自定义程序是用于自定义映像的函数，例如运行脚本或重新启动服务器。 

使用`customize`时 ： 
- 您可以使用多个自定义程序，但它们必须具有唯`name`一的 。
- 自定义程序按模板中指定的顺序执行。
- 如果一个自定义程序失败，则整个自定义组件将失败并报告错误。
- 强烈建议您在模板中使用脚本之前彻底测试脚本。 在您自己的 VM 上调试脚本将更容易。
- 不要将敏感数据放入脚本中。 
- 脚本位置需要公开访问，除非您正在使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)。

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

 
自定义部分是一个数组。 Azure 映像生成器将按顺序运行自定义程序。 任何自定义程序中的任何失败都将使生成过程失败。 
 
 
### <a name="shell-customizer"></a>外壳自定义程序

shell 自定义程序支持运行 shell 脚本，这些脚本必须是可公开访问的，以便 IB 访问它们。

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

操作系统支持：Linux 
 
自定义属性：

- **类型**= 外壳 
- **名称**- 用于跟踪自定义的名称 
- **脚本 Uri** - URI 到文件的位置 
- **内联**- shell 命令数组，用逗号分隔。
- **sha256Checksum** - 文件沙256校验和的值，您从本地生成此，然后图像生成器将检查和验证。
    * 要生成 sha256Checksum，请使用 Mac/Linux 上运行的终端：`sha256sum <fileName>`


要使用超级用户权限运行命令，必须用`sudo`预固定命令。

> [!NOTE]
> 使用 RHEL ISO 源运行 shell 自定义程序时，您需要确保在进行任何自定义之前，第一个自定义外壳处理与红帽授权服务器注册。 自定义完成后，脚本应取消向授权服务器注册。

### <a name="windows-restart-customizer"></a>Windows 重新启动自定义程序 
重新启动自定义程序允许您重新启动 Windows VM 并等待它重新联机，这允许您安装需要重新启动的软件。  

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

操作系统支持：窗口
 
自定义属性：
- **类型**： 窗口重新启动
- **重新启动命令**- 命令执行重新启动（可选）。 默认为 `'shutdown /r /f /t 0 /c \"packer restart\"'`。
- **重新启动检查命令**= 命令，以检查重新启动是否成功（可选）。 
- **重新启动超时**- 重新启动超时指定为大小和单位的字符串。 例如，（5`5m`分钟）或`2h`（2 小时）。 默认值为："5 米"

### <a name="linux-restart"></a>Linux 重启  
没有 Linux 重新启动自定义程序，但是，如果您正在安装驱动程序或需要重新启动的组件，则可以使用 Shell 自定义程序安装驱动程序并调用重新启动，则生成 VM 有 20 分钟的 SSH 超时。

### <a name="powershell-customizer"></a>PowerShell 定制器 
shell 自定义程序支持运行 PowerShell 脚本和内联命令，IB 必须公开访问这些脚本才能访问它们。

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

操作系统支持：Windows 和 Linux

自定义属性：

- **类型**= 电源外壳。
- **脚本 Uri** - URI 到 PowerShell 脚本文件的位置。 
- **内联**= 要运行的内联命令，用逗号分隔。
- **有效退出代码**– 可从脚本/内联命令返回的可选有效代码，这将避免脚本/内联命令的报告失败。
- **运行提升**– 可选、布尔，支持使用提升权限运行命令和脚本。
- **sha256Checksum** - 文件沙256校验和的值，您从本地生成此，然后图像生成器将检查和验证。
    * 要生成 sha256Checksum，请使用 Windows[获取哈希](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)上的 PowerShell


### <a name="file-customizer"></a>文件自定义器

文件自定义程序允许映像生成器从 GitHub 或 Azure 存储下载文件。 如果映像生成管道依赖于生成项目，则可以将文件自定义程序设置为从生成共享下载，并将项目移动到映像中。  

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

操作系统支持：Linux 和 Windows 

文件自定义器属性：

- **sourceUri** - 可访问的存储终结点，可以是 GitHub 或 Azure 存储。 您只能下载一个文件，而不能下载整个目录。 如果需要下载目录，请使用压缩文件，然后使用 Shell 或 PowerShell 自定义程序解压缩它。 
- **目标**= 这是完整的目标路径和文件名。 任何引用的路径和子目录都必须存在，使用命令程序或 PowerShell 自定义程序提前设置这些路径和子目录。 您可以使用脚本自定义程序创建路径。 

Windows 目录和 Linux 路径支持此功能，但有一些差异： 
- Linux操作系统 – 映像生成器可以写入的唯一路径是 /tmp。
- Windows = 没有路径限制，但路径必须存在。
 
 
如果尝试下载文件或将其放入指定的目录中时出错，则自定义步骤将失败，这将在自定义.log 中。

> [!NOTE]
> 文件自定义程序仅适用于小型文件下载，< 20MB。 对于较大的文件下载，请使用脚本或内联命令，使用代码下载文件，如 Linux`wget`或`curl`，Windows。 `Invoke-WebRequest`

文件自定义程序中的文件可以使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)从 Azure 存储下载。

### <a name="windows-update-customizer"></a>窗口更新自定义程序
此自定义程序基于社区[Windows 更新预配](https://packer.io/docs/provisioners/community-supported.html)器（由 Packer 社区维护的开源项目）构建。 Microsoft 使用映像生成器服务测试和验证预配程序，并将支持调查其问题，并致力于解决问题，但 Microsoft 并不正式支持开源项目。 有关 Windows 更新预配程序的详细文档和帮助，请参阅项目存储库。
 
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
操作系统支持：窗口

自定义属性：
- **类型**= Windows 更新。
- **搜索标准**- 可选，定义安装的更新类型（推荐、重要等）、"仅浏览"=0 和"已安装"=0（推荐）为默认值。
- **筛选器**= 可选，允许您指定筛选器以包括或排除更新。
- **更新限制**= 可选，定义可以安装多少更新，默认 1000。
 
 

### <a name="generalize"></a>通用化 
默认情况下，Azure 映像生成器还将在每个映像自定义阶段结束时运行"取消预配"代码，以"通用"映像。 通用化是设置映像以便重用映像以创建多个 VM 的过程。 对于 Windows VM，Azure 映像生成器使用 Sysprep。 对于 Linux，Azure 映像生成器运行"waagent-de预配"。 

命令映像生成器用户进行一化和通用化可能并不适合每种情况，因此 Azure 映像生成器将允许您根据需要自定义此命令。 

如果要迁移现有自定义项，并且使用不同的 Sysprep/waagent 命令，则可以使用映像生成器泛型命令，如果 VM 创建失败，请使用您自己的 Sysprep 或 waagent 命令。

如果 Azure 映像生成器成功创建 Windows 自定义映像，并且从该映像创建 VM，则发现 VM 创建失败或未成功完成，则需要查看 Windows Server Sysprep 文档，或使用Windows 服务器 Sysprep 客户服务支持团队，他们可以对正确的 Sysprep 使用情况进行故障排除并提供建议。


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
#### <a name="default-linux-deprovision-command"></a>默认 Linux 取消预配命令

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>重写命令
要重写这些命令，请使用 PowerShell 或 Shell 脚本预配器创建具有确切文件名的命令文件，并将它们放在正确的目录中：

* 窗口：c：\取消预配脚本.ps1
* Linux： /tmp/取消预配脚本.sh

映像生成器将读取这些命令，这些命令被写入 AIB 日志，"自定义.log"。 有关如何收集日志[的疑难解答](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs)。
 
## <a name="properties-distribute"></a>属性：分发

Azure 映像生成器支持三个分发目标： 

- **托管映像**- 托管映像。
- **共享图像**- 共享图像库。
- **VHD** - 存储帐户中的 VHD。

您可以将映像分发到同一配置中的两个目标类型，请参阅[示例](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)。

由于可以有多个目标要分发到 ，因此 Image Builder 会维护可通过查询 访问的每个分发目标的状态`runOutputName`。  `runOutputName`是可以查询发布后分发的对象，以获取有关该分布的信息。 例如，您可以查询 VHD 的位置、映像版本复制到的区域或创建 SIG 图像版本的区域。 这是每个分发目标的属性。 必须`runOutputName`对每个分发目标是唯一的。 下面是一个示例，这是查询共享图像库分发：

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

### <a name="distribute-managedimage"></a>分发：托管图像

映像输出将是托管映像资源。

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
- **类型**= 托管图像 
- **映像 Id** = 目标图像的资源 ID，预期格式：/\<订阅/订阅 id>/资源组/\<目标资源组名称>/提供程序/Microsoft.\<计算/图像/图像名称>
- **位置**- 托管映像的位置。  
- **运行输出名称**= 标识分布的唯一名称。  
- **项目标记**- 可选用户指定键值对标记。
 
 
> [!NOTE]
> 目标资源组必须存在。
> 如果希望将映像分发到其他区域，它将增加部署时间。 

### <a name="distribute-sharedimage"></a>分发：共享图像 
Azure 共享映像库是一种新的映像管理服务，允许管理映像区域复制、版本控制和共享自定义映像。 Azure 映像生成器支持使用此服务进行分发，因此可以将映像分发到共享映像库支持的区域。 
 
共享图像库由： 
 
- 库 - 用于多个共享图像的容器。 库部署在一个区域中。
- 图像定义 - 图像的概念分组。 
- 映像版本 - 这是用于部署 VM 或缩放集的图像类型。 图像版本可以复制到需要部署 VM 的其他地区。
 
在可以分发到图像库之前，必须创建库和图像定义，请参阅[共享图像](shared-images.md)。 

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

为共享图像库分发属性：

- **类型**- 共享图像  
- **库 ImageId** = 共享图像库的 ID。 格式为\<：/订阅/订阅 id>/资源组\</资源组名称>/提供商/Microsoft.计算/库图\</共享 ImageGallery 名称>/图像/\<图像库名称>。
- **运行输出名称**= 标识分布的唯一名称。  
- **项目标记**- 可选用户指定键值对标记。
- **复制区域**- 用于复制的区域数组。 其中一个区域必须是部署库的区域。
 
> [!NOTE]
> 可以在其他区域中使用 Azure 映像生成器到库，但 Azure 映像生成器服务将需要在数据中心之间传输映像，这需要更长的时间。 图像生成器将根据单调整数自动对图像进行版本控制，您当前无法指定它。 

### <a name="distribute-vhd"></a>分发： VHD  
您可以输出到 VHD。 然后，可以复制 VHD，并使用它发布到 Azure 市场广场，或使用 Azure 堆栈。  

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
 
操作系统支持：Windows 和 Linux

分发 VHD 参数：

- **类型**- VHD。
- **运行输出名称**= 标识分布的唯一名称。  
- **标记**- 可选用户指定键值对标记。
 
Azure 映像生成器不允许用户指定存储帐户位置，但您可以查询 的状态`runOutputs`以获取该位置。  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> 创建 VHD 后，请尽快将其复制到其他位置。 VHD 存储在将映像模板提交到 Azure 映像生成器服务时创建的临时资源组中的存储帐户中。 如果删除图像模板，则将丢失 VHD。 
 
## <a name="next-steps"></a>后续步骤

[Azure 映像生成器 GitHub](https://github.com/danielsollondon/azvmimagebuilder)中对于不同方案的示例 .json 文件。
 
