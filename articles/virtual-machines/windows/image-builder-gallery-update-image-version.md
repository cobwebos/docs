---
title: '使用 Azure 映像生成器 (预览版，通过现有映像版本创建新的映像版本) '
description: 使用 Windows 中的 Azure 映像生成器从现有映像版本创建新的 VM 映像版本。
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.openlocfilehash: 7515e0a39d1cf0da74d2a23457443e96716b4275
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335946"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-windows"></a>预览：使用 Windows 中的 Azure 映像生成器在现有映像版本中创建新的 VM 映像版本

本文介绍如何使用 [共享映像库](shared-image-galleries.md)中的现有映像版本，对其进行更新，并将其作为新的映像版本发布到库。

我们将使用一个示例 .json 模板来配置映像。 此处使用的 json 文件是： [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json)。 

> [!IMPORTANT]
> Azure 映像生成器目前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>注册功能
若要在预览期间使用 Azure 映像生成器，需要注册新功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

检查功能注册的状态。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

检查注册。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
```

如果未显示“已注册”，请运行以下命令：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>设置变量和访问权限

如果使用了 [创建图像并将其分发给共享映像库](image-builder-gallery.md) 来创建共享映像库，则已创建了所需的变量。 如果没有，请设置要用于此示例的一些变量。

对于预览，映像生成器仅支持在与源托管映像相同的资源组中创建自定义映像。 将此示例中的资源组名称更新为与源托管映像相同的资源组。

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

为你的订阅 ID 创建变量。 可使用 `az account show | grep id` 获取。

```azurecli-interactive
subscriptionID=<Subscription ID>
```

获取要更新的映像版本。

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>创建用户分配的标识，并在资源组上设置权限
在上一示例中设置用户标识后，只需获取其资源 ID，然后将其追加到模板。

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

如果已经有了自己的共享映像库，但没有按前面的示例进行操作，则需要为映像生成器分配权限以访问资源组，以便可以访问该资源组。 请查看 [创建映像和分发到共享图像库](image-builder-gallery.md) 示例中的步骤。


## <a name="modify-helloimage-example"></a>修改 helloImage 示例
你可以通过在以下位置打开 json 文件来查看我们将要使用的示例：使用[图像生成器模板参考](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) [helloImageTemplateforSIGfromSIG.js](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) 。 


下载此 json 示例，并将其配置为你的变量。 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>创建映像

将映像配置提交给 VM 映像生成器服务。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

启动映像生成。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

等待映像已生成并复制，然后再继续下一步。


## <a name="create-the-vm"></a>创建 VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>验证自定义
使用创建 VM 时设置的用户名和密码创建与 VM 的远程桌面连接。 在 VM 中，打开命令提示符并键入以下内容：

```console
dir c:\
```

现在应会看到两个目录：
- `buildActions` 在第一个映像版本中创建的。
- `buildActions2` 这是在更新第一个映像版本时创建的，以创建第二个映像版本。


## <a name="next-steps"></a>后续步骤

若要详细了解本文中使用的 json 文件的组件，请参阅 [图像生成器模板参考](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
