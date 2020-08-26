---
title: 使用 Azure CLI 配置 Azure 映像生成器服务权限
description: 使用 Azure CLI 配置 Azure VM 映像生成器服务的要求，包括权限和特权
author: cynthn
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 58bbe01c8de0bbe606f4fc428032cd213f05d386
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068107"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>使用 Azure CLI 配置 Azure 映像生成器服务权限

Azure 映像生成器服务需要在生成映像之前配置权限和特权。 以下部分详细说明了如何使用 Azure CLI 配置可能的方案。

> [!IMPORTANT]
> Azure 映像生成器目前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>注册功能

首先，必须注册 Azure 映像生成器服务。 注册将授予服务创建、管理和删除暂存资源组的权限。 服务还具有添加资源所需的资源组的权限。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>创建 Azure 用户分配的托管标识

Azure 映像生成器要求创建[azure 用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)。 Azure 映像生成器使用用户分配的托管标识来读取映像、写入映像以及访问 Azure 存储帐户。 你向标识授予在你的订阅中执行特定操作的权限。

> [!NOTE]
> 以前，Azure 映像生成器使用 Azure 映像生成器服务主体名称 (SPN) 为映像资源组授予权限。 将弃用使用 SPN。 改为使用用户分配的托管标识。

以下示例演示如何创建 Azure 用户分配的托管标识。 替换占位符设置以设置变量。

| 设置 | 描述 |
|---------|-------------|
| \<Resource group\> | 要在其中创建用户分配的托管标识的资源组。 |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

有关 Azure 用户分配的标识的详细信息，请参阅[azure 用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)文档，了解如何创建标识。

## <a name="allow-image-builder-to-distribute-images"></a>允许图像生成器分发图像

要使 Azure 映像生成器 (托管映像/共享映像库) 分发映像，必须允许 Azure 映像生成器服务将图像注入这些资源组。 若要授予所需的权限，需要创建用户分配的托管标识，并在生成映像的资源组上授予其权限。 Azure**映像生成器无权**访问订阅中其他资源组中的资源。 你需要执行显式操作以允许访问，以避免你的生成失败。

无需向用户分配的托管标识参与者授予资源组的权限即可分发映像。 但是，用户分配的托管标识需要 `Actions` 分发资源组中的以下 Azure 权限：

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

如果要分发到共享映像库，还需要：

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>自定义现有映像的权限

要使 Azure 映像生成器能 (托管映像/共享映像库) 从源自定义映像生成映像，必须允许 Azure 映像生成器服务将图像读入这些资源组。 若要授予所需的权限，需要创建用户分配的托管标识，并向其授予对映像所在的资源组的权限。

从现有自定义映像生成：

```Actions
Microsoft.Compute/galleries/read
```

从现有的共享映像库版本生成：

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>自定义 Vnet 的映像的权限

Azure 映像生成器可以在订阅中部署和使用现有 VNET，从而允许自定义访问连接的资源。

无需向用户分配的托管标识参与者授予资源组，即可将 VM 部署到现有的 VNET。 但是，用户分配的托管标识需要对 `Actions` VNET 资源组具有以下 Azure 权限：

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>创建 Azure 角色定义

以下示例根据前面部分中所述的操作创建 Azure 角色定义。 示例在资源组级别应用。 评估并测试示例是否精细满足你的要求。 对于你的方案，你可能需要将其优化到特定的共享映像库。

图像操作允许进行读写。 确定适合您的环境的内容。 例如，创建角色以允许 Azure 映像生成器从资源组中读取映像*示例-rg-1*并将映像写入资源组*示例-rg-2*。

### <a name="custom-image-azure-role-example"></a>自定义映像 Azure 角色示例

以下示例创建一个 Azure 角色以使用和分发源自定义映像。 然后，将自定义角色授予 Azure 映像生成器的用户分配的托管标识。

若要简化示例中值的替换，请先设置以下变量。 替换占位符设置以设置变量。

| 设置 | 描述 |
|---------|-------------|
| \<Subscription ID\> | Azure 订阅 ID |
| \<Resource group\> | 自定义映像的资源组 |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>现有的 VNET Azure 角色示例

以下示例创建一个 Azure 角色以使用和分发现有的 VNET 映像。 然后，将自定义角色授予 Azure 映像生成器的用户分配的托管标识。

若要简化示例中值的替换，请先设置以下变量。 替换占位符设置以设置变量。

| 设置 | 描述 |
|---------|-------------|
| \<Subscription ID\> | Azure 订阅 ID |
| \<Resource group\> | VNET 资源组 |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>使用托管标识访问 Azure 存储

如果要无缝使用 Azure 存储进行身份验证并使用私有容器，azure 映像生成器 Azure 需要使用用户分配的托管标识。 Azure 映像生成器使用该标识对 Azure 存储进行身份验证。

> [!NOTE]
> Azure 映像生成器仅在映像模板提交时使用标识。 生成 VM 在映像生成过程中无权访问标识。

使用 Azure CLI 创建用户分配的托管标识。

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

在映像生成器模板中，需要提供用户分配的托管标识。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

替换以下占位符设置：

| 设置 | 描述 |
|---------|-------------|
| \<Region\> | 模板区域 |
| \<Resource group\> | 资源组 |
| \<Storage account container\> | 存储帐户容器名称 |
| \<Subscription ID\> | Azure 订阅 |

有关使用用户分配的托管标识的详细信息，请参阅[创建自定义映像，该映像将使用 Azure 用户分配的托管标识来无缝访问 Azure 存储的文件](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage#create-a-custom-image-that-will-use-an-azure-user-assigned-managed-identity-to-seemlessly-access-files-azure-storage)。 快速入门介绍如何创建和配置用户分配的托管标识以访问存储帐户。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[Azure 映像生成器概述](image-builder-overview.md)。