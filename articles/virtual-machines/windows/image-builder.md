---
title: 使用 Azure 映像生成器创建 Windows VM（预览）
description: 使用 Azure 映像生成器创建 Windows VM。
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: e82d82dac833f7455e3d83d7e11c0c57c4eea816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238804"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>预览：使用 Azure 映像生成器创建 Windows VM

本文将向您展示如何使用 Azure VM 映像生成器创建自定义的 Windows 映像。 本文中的示例使用[自定义器](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize)来自定义映像：
- PowerShell （脚本） - 下载并运行[PowerShell 脚本](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)。
- Windows 重新启动 - 重新启动 VM。
- PowerShell（内联） - 运行特定命令。 在此示例中，它使用 在 VM 上创建`mkdir c:\\buildActions`一个目录。
- 文件 - 将文件从 GitHub 复制到 VM 上。 此示例[将index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)复制到`c:\buildArtifacts\index.html`VM 上。

还可以指定 。 `buildTimeoutInMinutes` 默认值为 240 分钟，您可以增加生成时间以允许较长的运行生成。

我们将使用示例 .json 模板来配置映像。 我们使用的 .json 文件在这里：[你好ImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure 映像生成器当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="register-the-features"></a>注册功能

要在预览期间使用 Azure 映像生成器，需要注册新功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

检查功能注册的状态。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

检查您的注册。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

如果他们不说已注册，则运行以下内容：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables"></a>设置变量

我们将反复使用一些信息片段，因此我们将创建一些变量来存储该信息。


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

为订阅 ID 创建变量。 您可以使用 获取此`az account show | grep id`功能。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>创建资源组
此资源组用于存储映像配置模板项目和映像。


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>设置资源组的权限

授予图像生成器"参与者"在资源组中创建映像的权限。 如果没有这一点，映像生成将失败。 

该`--assignee`值是映像生成器服务的应用注册 ID。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>下载映像配置模板示例

已创建参数化映像配置模板，供您尝试。 下载示例 .json 文件，然后使用以前设置的变量对其进行配置。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

您可以在终端中使用文本编辑器（如`vi`）修改此示例。

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 对于源映像，必须始终[指定版本](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)，不能使用`latest`。
> 如果添加或更改映像分发到的资源组，则必须在资源组上[设置权限](#set-permissions-on-the-resource-group)。
 
## <a name="create-the-image"></a>创建映像

将映像配置提交到 VM 映像生成器服务

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

完成后，这将返回成功消息返回到控制台，并在 中创建 。 `Image Builder Configuration Template` `$imageResourceGroup` 如果启用"显示隐藏类型"，则可以在 Azure 门户中的资源组中看到此资源。

在后台，映像生成器还将在订阅中创建暂存资源组。 此资源组用于映像生成。 它将采用此格式：`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> 不能直接删除暂存资源组。 首先删除图像模板项目，这将导致删除暂存资源组。

如果服务在映像配置模板提交期间报告失败：
-  查看这些[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting)步骤。 
- 在重试提交之前，您需要使用以下代码段删除模板。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>启动映像生成
使用[az 资源调用操作](/cli/azure/resource#az-resource-invoke-action)启动映像生成过程。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

等待生成完成。 这可能需要大约 15 分钟。

如果您遇到任何错误，请查看这些[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)步骤。


## <a name="create-the-vm"></a>创建 VM

使用生成的映像创建 VM。 将*\<密码>* 替换为 VM`aibuser`上的密码。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>验证自定义

使用创建 VM 时设置的用户名和密码创建与 VM 的远程桌面连接。 在 VM 中，打开 cmd 提示并键入：

```console
dir c:\
```

您应该会看到在映像自定义期间创建的这两个目录：
- 生成操作
- 生成工件

## <a name="clean-up"></a>清除

完成后，删除资源。

### <a name="delete-the-image-builder-template"></a>删除映像生成器模板

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>删除图像资源组

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>后续步骤

要了解有关本文中使用的 .json 文件组件的更多内容，请参阅[图像生成器模板引用](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
