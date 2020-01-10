---
title: 创建 Azure 映像生成器模板（预览版）
description: 了解如何创建用于 Azure 映像生成器的模板。
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 4a411603ca5c3c79da0d596396d8fde80b568af2
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763073"
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
             },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>类型和 API 版本

`type` 是必须 `"Microsoft.VirtualMachineImages/imageTemplates"`的资源类型。 随着 API 的更改，`apiVersion` 会随时间而变化，但应 `"2019-05-01-preview"` 用于预览。

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


```json
    "location": "<region>",
```

## <a name="tags"></a>标记

这些是可为生成的映像指定的键/值对。

## <a name="depends-on-optional"></a>取决于（可选）

此可选部分可用于确保在继续操作之前完成依赖项。 

```json
    "dependsOn": [],
```

有关详细信息，请参阅[定义资源依赖项](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)。

## <a name="identity"></a>身份标识
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

"`source`" 部分包含有关映像生成器将使用的源映像的信息。

API 需要一个为映像生成定义源的 "SourceType"，当前有三种类型：
- ISO-源是 RHEL ISO 时使用此项。
- PlatformImage-表示源映像是 Marketplace 映像。
- ManagedImage-从常规托管映像开始时使用此项。
- SharedImageVersion-当使用共享映像库中的映像版本作为源时使用此项。

### <a name="iso-source"></a>ISO 源

Azure 映像生成器仅支持使用已发布的 Red Hat Enterprise Linux 7. x 二进制 DVD Iso，用于预览。 映像生成器支持：
- RHEL 7.3 
- RHEL 7。4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

若要获取 `sourceURI` 和 `sha256Checksum` 值，请转到 `https://access.redhat.com/downloads`，然后选择产品**Red Hat Enterprise Linux**和支持的版本。 

在**Red Hat Enterprise Linux 服务器的安装程序和映像**的列表中，需要复制 Red Hat Enterprise Linux 7、WINDOWS 二进制 DVD 和校验和的链接。

> [!NOTE]
> 链接的访问令牌会定期刷新，因此，每次需要提交模板时，都必须检查 RH 链接地址是否已更改。
 
### <a name="platformimage-source"></a>PlatformImage 源 
Azure 映像生成器支持以下 Azure Marketplace 映像：
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7。6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


此处的属性与使用 AZ CLI 创建 VM 的属性相同，请运行以下内容获取属性： 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> 版本不能是 "最新的"，您必须使用上面的命令获取版本号。 

### <a name="managedimage-source"></a>ManagedImage 源

将源映像设置为通用 VHD 或 VM 的现有托管映像。 源托管映像必须是受支持的操作系统，并且必须与 Azure 映像生成器模板位于同一区域。 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` 应为托管映像的 ResourceId。 使用 `az image list` 列出可用图像。


### <a name="sharedimageversion-source"></a>SharedImageVersion 源
将源映像设置为共享映像库中的现有映像版本。 映像版本必须是受支持的操作系统，并且必须将映像复制到 Azure 映像生成器模板所在的同一区域。 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` 应为映像版本的 ResourceId。 使用[az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list)列出映像版本。

## <a name="properties-buildtimeoutinminutes"></a>属性： buildTimeoutInMinutes

默认情况下，映像生成器将运行240分钟。 之后，无论映像生成是否完成，它都将超时和停止。 如果遇到超时，你将看到类似于下面的错误：

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

如果未指定 buildTimeoutInMinutes 值或将其设置为0，则将使用默认值。 可以增加或减少值，最大值为960mins （16hrs）。 对于 Windows，我们建议不要在下面的60分钟内将其设置为。 如果发现遇到超时，请检查[日志](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)，以查看自定义步骤是否正在等待用户输入之类的内容。 

如果你发现需要更多的时间来完成自定义，请将此项设置为你所需的时间，但会出现一些开销。 但是，不要将它设置得太高，因为可能需要等待它超时，然后才会看到错误。 


## <a name="properties-customize"></a>属性：自定义

图像生成器支持多个 "定制器"。 定制员是用于自定义映像的功能，如运行脚本或重新启动服务器。 

使用 `customize`时： 
- 可以使用多个定制员，但必须具有唯一的 `name`。
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
                "scriptUri": "<path to script>"
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
            "scriptUri": "<link to script>"        
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
 
> [!NOTE]
> 使用 RHEL ISO 源运行 shell 定制器时，需要确保第一个自定义项 shell 在进行任何自定义之前，使用 Red Hat 授权服务器进行注册。 自定义完成后，脚本应向授权服务器取消注册。

### <a name="windows-restart-customizer"></a>Windows 重新启动定制器 
重新启动定制器允许你重新启动 Windows VM 并等待它重新联机，这允许你安装需要重新启动的软件。  

```json 
     "customize": [ 
         {
            "type": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

OS 支持： Windows
 
自定义属性：
- **类型**： WindowsRestart
- **restartCommand** -执行重新启动的命令（可选）。 默认为 `'shutdown /r /f /t 0 /c \"packer restart\"'`。
- **restartCheckCommand** –用于检查重新启动是否成功的命令（可选）。 
- **restartTimeout** -以数量级和 unit 的字符串形式指定的重新启动超时。 例如，`5m` （5分钟）或 `2h` （2小时）。 默认值为： "5 分钟"


### <a name="powershell-customizer"></a>PowerShell 定制器 
Shell 定制器支持运行 PowerShell 脚本和内联命令，这些脚本必须可公开访问，IB 才能访问这些脚本。

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

OS 支持： Windows 和 Linux

自定义属性：

- **类型**– PowerShell。
- **scriptUri** -PowerShell 脚本文件所在位置的 URI。 
- **内联**–要运行的内联命令（用逗号分隔）。
- **valid_exit_codes** –可从 script/inline 命令返回的可选有效代码，这将避免在 script/inline 命令中报告失败。

### <a name="file-customizer"></a>文件定制器

文件定制器允许图像生成器从 GitHub 或 Azure 存储下载文件。 如果有依赖于生成项目的映像生成管道，则可以将文件定制器设置为从生成共享下载，并将项目移动到映像。  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
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
> 文件定制器仅适用于小型文件下载，< 20MB。 对于较大的文件下载，请使用脚本或内联命令，使用代码下载文件，如 Linux `wget` 或 `curl`、Windows、`Invoke-WebRequest`。

可以使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)从 Azure 存储下载文件定制器中的文件。

### <a name="generalize"></a>通用化 
默认情况下，Azure 映像生成器还将在每个映像自定义阶段结束时运行 "取消设置" 代码，以 "通用化" 映像。 通用化是设置映像的过程，以便可以重复使用该映像创建多个虚拟机。 对于 Windows Vm，Azure 映像生成器使用 Sysprep。 对于 Linux，Azure 映像生成器运行 "waagent-取消预配"。 

要通用化的命令图像生成器用户可能不适合每种情况，因此，如果需要，Azure 映像生成器将允许您自定义此命令。 

如果要迁移现有自定义，并且使用的是不同的 Sysprep/waagent 命令，则可以使用映像生成器一般命令; 如果 VM 创建失败，请使用自己的 Sysprep 或 waagent 命令。

如果 Azure 映像生成器成功创建了一个 Windows 自定义映像，并且你从该映像创建了一个 VM，然后发现 VM 创建失败或未成功完成，你将需要查看 Windows Server Sysprep 文档或使用Windows Server Sysprep 客户服务支持团队，可对正确的 Sysprep 使用进行故障排除和建议。


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

* Windows: c:\DeprovisioningScript.ps1
* Linux：/tmp/DeprovisioningScript.sh

映像生成器将读取这些命令，这些命令将写出到 AIB 日志 "自定义日志"。 有关如何收集日志的[疑难解答](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs)，请参阅。
 
## <a name="properties-distribute"></a>属性：分布

Azure 映像生成器支持三个分发目标： 

- **managedImage** -托管映像。
- **sharedImage** -共享图像库。
- **Vhd** -存储帐户中的 vhd。

您可以在同一配置中将图像分发到这两个目标类型，请参阅[示例](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)。

由于可以将多个目标分发到，因此，映像生成器会为每个可通过查询 `runOutputName`来访问的分发目标维护状态。  `runOutputName` 是一个对象，您可以在分发分发中查询该分发的相关信息。 例如，你可以查询 VHD 的位置或将映像版本复制到的区域。 这是每个分布目标的属性。 每个分发目标的 `runOutputName` 必须是唯一的。
 
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
- **imageId** –目标映像的资源 ID，格式应为：/Subscriptions/\<subscriptionId >/ResourceGroups/\<destinationResourceGroupName >/Providers/Microsoft.Compute/images/\<imageName >
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
- **galleryImageId** –共享映像库的 ID。 格式为：/subscriptions/\<subscriptionId >/resourceGroups/\<resourceGroupName >/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName >/images/\<imageGalleryName >。
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
 
Azure 映像生成器不允许用户指定存储帐户位置，但你可以查询 `runOutputs` 的状态以获取位置。  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> 创建 VHD 后，请尽快将其复制到其他位置。 VHD 存储在临时资源组中的存储帐户中，该资源组是在将映像模板提交到 Azure 映像生成器服务时创建的。 如果删除了映像模板，则会丢失 VHD。 
 
## <a name="next-steps"></a>后续步骤

[Azure 映像生成器 GitHub](https://github.com/danielsollondon/azvmimagebuilder)中提供了适用于不同方案的示例 json 文件。
 
 
 
 
 
 
 
 
 
 
