---
title: 使用 Azure 映像生成器创建 Windows VM (预览版)
description: 使用 Azure 映像生成器创建 Windows VM。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 103ec3c9ee4bd6b3b83408b0f9958a22d3a22ae1
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261058"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>预览版：使用 Azure 映像生成器创建 Windows VM

本文介绍如何使用 Azure VM 映像生成器创建自定义的 Windows 映像。 本文中的示例使用三[个不同的](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize)自定义程序来自定义映像:
- PowerShell (ScriptUri)-下载并运行[powershell 脚本](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)。
- Windows 重新启动-重新启动 VM。
- PowerShell (内联)-运行特定命令。 在此示例中, 它使用`mkdir c:\\buildActions`在 VM 上创建一个目录。
- 文件-将 GitHub 中的文件复制到 VM。 此示例将[index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)复制`c:\buildArtifacts\index.html`到 VM 上的。

我们将使用示例 json 模板来配置映像。 我们使用的 json 文件是: [helloImageTemplateWin](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json)。 


> [!IMPORTANT]
> Azure 映像生成器目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="register-the-features"></a>注册功能

若要在预览期间使用 Azure 映像生成器, 需要注册新功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

检查功能注册的状态。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

检查你的注册。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

如果未注册, 请运行以下内容:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="create-a-resource-group"></a>创建资源组

我们将重复使用某些信息, 因此我们将创建一些变量来存储该信息。

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

为订阅 ID 创建一个变量。 你可以使用`az account show | grep id`获取此。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

创建资源组。

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

为映像生成器授予在该资源组中创建资源的权限。 `--assignee`该值是映像生成器服务的应用注册 ID。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>下载. json 示例

下载示例 json 文件, 并将其配置为创建的变量。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>创建映像

将映像配置提交给 VM 映像生成器服务

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

启动映像生成。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

等待生成完成。 这可能需要大约15分钟。

## <a name="create-the-vm"></a>创建 VM

使用生成的映像创建 VM。 将 *\<password >* 替换为 VM 上的你`aibuser`自己的密码。

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

使用创建 VM 时设置的用户名和密码创建与 VM 的远程桌面连接。 在 VM 中, 打开 cmd 提示符并键入:

```console
dir c:\
```

在映像自定义期间, 应看到这两个目录是创建的:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>清理

完成后, 删除资源。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>后续步骤

若要详细了解本文中使用的 json 文件的组件, 请参阅[图像生成器模板参考](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

