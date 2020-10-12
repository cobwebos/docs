---
title: 使用 PowerShell 配置 Azure 映像生成器服务权限
description: 使用 PowerShell 配置 Azure VM 映像生成器服务的要求，包括权限和特权
author: danielsollondon
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: cfe3efc77e065ac3685b72d0eab501034609b59b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88068114"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>使用 PowerShell 配置 Azure 映像生成器服务权限

Azure 映像生成器服务需要在生成映像之前配置权限和特权。 以下部分详细说明了如何使用 PowerShell 配置可能的方案。

> [!IMPORTANT]
> Azure 映像生成器目前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>注册功能

首先，必须注册 Azure 映像生成器服务。 注册将授予服务创建、管理和删除暂存资源组的权限。 服务还具有添加资源所需的资源组的权限。

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>创建 Azure 用户分配的托管标识

Azure 映像生成器要求创建 [azure 用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)。 Azure 映像生成器使用用户分配的托管标识来读取映像、写入映像以及访问 Azure 存储帐户。 你向标识授予在你的订阅中执行特定操作的权限。

> [!NOTE]
> 以前，Azure 映像生成器使用 Azure 映像生成器服务主体名称 (SPN) 为映像资源组授予权限。 将弃用使用 SPN。 改为使用用户分配的托管标识。

以下示例演示如何创建 Azure 用户分配的托管标识。 替换占位符设置以设置变量。

| 设置 | 说明 |
|---------|-------------|
| \<Resource group\> | 要在其中创建用户分配的托管标识的资源组。 |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

有关 Azure 用户分配的标识的详细信息，请参阅 [azure 用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) 文档，了解如何创建标识。

## <a name="allow-image-builder-to-distribute-images"></a>允许图像生成器分发图像

要使 Azure 映像生成器 (托管映像/共享映像库) 分发映像，必须允许 Azure 映像生成器服务将图像注入这些资源组。 若要授予所需的权限，需要创建用户分配的托管标识，并在生成映像的资源组上授予其权限。 Azure **映像生成器无权** 访问订阅中其他资源组中的资源。 你需要执行显式操作以允许访问，以避免你的生成失败。

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

图像操作允许进行读写。 确定适合您的环境的内容。 例如，创建角色以允许 Azure 映像生成器从资源组中读取映像 *示例-rg-1* 并将映像写入资源组 *示例-rg-2*。

### <a name="custom-image-azure-role-example"></a>自定义映像 Azure 角色示例

以下示例创建一个 Azure 角色以使用和分发源自定义映像。 然后，将自定义角色授予 Azure 映像生成器的用户分配的托管标识。

若要简化示例中值的替换，请先设置以下变量。 替换占位符设置以设置变量。

| 设置 | 说明 |
|---------|-------------|
| \<Subscription ID\> | Azure 订阅 ID |
| \<Resource group\> | 自定义映像的资源组 |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>现有的 VNET Azure 角色示例

以下示例创建一个 Azure 角色以使用和分发现有的 VNET 映像。 然后，将自定义角色授予 Azure 映像生成器的用户分配的托管标识。

若要简化示例中值的替换，请先设置以下变量。 替换占位符设置以设置变量。

| 设置 | 说明 |
|---------|-------------|
| \<Subscription ID\> | Azure 订阅 ID |
| \<Resource group\> | VNET 资源组 |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure 映像生成器概述](image-builder-overview.md)。