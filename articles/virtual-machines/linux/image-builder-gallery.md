---
title: '使用 Azure 映像生成器 & 适用于 Linux Vm (预览版的共享映像库) '
description: 了解如何使用 Azure 映像生成器和 Azure CLI 来创建共享映像库中的映像版本，然后全局分布该图像。
author: cynthn
ms.author: cynthn
ms.date: 05/05/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: bab5a86cdf8e3fa6b17866fe4088b28ed3ece6f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91307290"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>预览版：创建 Linux 映像并将其分发到共享映像库 

本文介绍如何使用 Azure 映像生成器和 Azure CLI，在[共享映像库](../windows/shared-image-galleries.md)中创建映像版本，然后全局分发此映像。 还可以使用 [Azure PowerShell](../windows/image-builder-gallery.md).来实现此目的。


我们将使用一个示例 .json 模板来配置映像。 我们将使用的 .json 文件位于：[helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json)。 

此模板使用 [sharedImage](image-builder-json.md#distribute-sharedimage) 作为模板的 `distribute` 部分的值，以便将映像分发到共享映像库。

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

我们将重复使用某些信息，因此我们将创建一些变量来存储这些信息。

对于预览，映像生成器仅支持在与源托管映像相同的资源组中创建自定义映像。 将此示例中的资源组名称更新为与源托管映像相同的资源组。

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

为你的订阅 ID 创建变量。 可使用 `az account show | grep id` 获取。

```azurecli-interactive
subscriptionID=<Subscription ID>
```

创建资源组。

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>创建用户分配的标识，并在资源组上设置权限
映像生成器将使用提供的[用户标识](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)，来将映像注入到 Azure 共享映像库 (SIG)。 在此示例中，你将创建一个 Azure 角色定义，其中包含将映像分发到 SIG 的精细操作。 然后将此角色定义分配给用户标识。

```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>创建映像定义和库

若要将映像生成器用于共享映像库，需要具有现有的映像库和映像定义。 映像生成器无法创建映像库和映像定义。

如果还没有可使用的库和映像定义，请先创建它们。 首先创建映像库。

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

然后，创建映像定义。

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>下载并配置 .json

下载 .json 模板，并使用你的变量对它进行配置。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>创建映像版本

在接下来的部分中，将在库中创建映像版本。 

将映像配置提交到 Azure 映像生成器服务。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

启动映像生成。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

创建映像并将其复制到这两个区域，这可能需要一段时间。 等待至此部分完成，然后再继续创建 VM。


## <a name="create-the-vm"></a>创建 VM

从 Azure 映像生成器创建的映像版本创建 VM。

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

通过 SSH 登录到 VM。

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

建立 SSH 连接后，应会立即看到映像已使用当天的一个消息进行了自定义！

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>清理资源

如果现在想要尝试重新自定义映像版本，以创建同一映像的新版本，请跳过后续步骤，并继续学习[使用 Azure 映像生成器创建其他映像版本](image-builder-gallery-update-image-version.md)。


这样会删除已创建的映像以及所有其他的资源文件。 删除这些资源前，请确保已完成此部署。

删除映像库资源时，需要先删除所有版本的映像，然后才能删除用于创建它们的映像定义。 若要删除库，首先需要删除库中的所有映像定义。

删除映像生成器模板。

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

删除权限 asssignments、角色和标识
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

获取由映像生成器创建的映像版本，该版本始终以 `0.` 开头，然后删除该映像版本

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


删除映像定义。

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

删除库。

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

删除该资源组。

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 共享映像库](shared-image-galleries.md)。
