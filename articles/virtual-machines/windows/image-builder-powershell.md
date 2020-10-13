---
title: 使用 PowerShell 通过 Azure 映像生成器创建 Windows VM
description: 使用 Azure 映像生成器 PowerShell 模块创建 Windows VM。
author: cynthn
ms.author: cynthn
ms.date: 06/17/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a221ba8fe14db37729183774197bfc2db8bf2baa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328099"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>预览：使用 Azure 映像生成器创建 Windows VM 使用 PowerShell

本文演示如何使用 Azure VM 映像生成器 PowerShell 模块创建自定义的 Windows 映像。

> [!CAUTION]
> Azure 映像生成器目前提供公共预览版。 此预览版本未提供服务级别协议。 对于生产型工作负荷，不建议使用该预览版。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

如果选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块，并使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 尽管**ImageBuilder**和**ManagedServiceIdentity** PowerShell 模块处于预览阶段，但你必须使用 `Install-Module` cmdlet 和参数分别安装这些模块。 `AllowPrerelease` 当这些 PowerShell 模块公开上市后，它们将成为未来 Az PowerShell 模块版本的一部分，并从 Azure Cloud Shell 中的本机提供。

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定订阅。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>注册功能

如果这是你首次在预览期间使用 Azure 映像生成器，请注册新的 **VirtualMachineTemplatePreview** 功能。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

检查功能注册的状态。

> [!NOTE]
> 在将更改为之前， **RegistrationState** 可能处于 `Registering` 状态几分钟 `Registered` 。 等到状态 **注册** 后再继续。

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

如果 Azure 订阅尚未注册，请注册以下资源提供程序以供使用。

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>定义变量

你将重复使用几条信息。 创建变量以存储信息。

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

为你的 Azure 订阅 ID 创建一个变量。 若要确认 `subscriptionID` 变量是否包含您的订阅 ID，可以在下面的示例中运行第二行。

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 [Azure 资源组](../../azure-resource-manager/management/overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

下面的示例创建一个资源组，该资源组基于在 `$location` 变量中指定的区域中 `$imageResourceGroup` 变量中的名称。 此资源组用于存储映像配置模板项目和映像。

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>创建用户标识和设置角色权限

使用以下示例，授予 Azure 映像生成器在指定资源组中创建映像的权限。 如果没有此权限，则映像生成过程将不会成功完成。

为角色定义和标识名称创建变量。 这些值必须唯一。

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

创建用户标识。

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

将标识资源和主体 Id 存储在变量中。

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>为标识分配权限以分发映像

下载 json 配置文件，并根据本文中定义的设置对其进行修改。

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

创建角色定义。

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

向映像生成器服务主体授予角色定义。

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> 如果收到错误： "_AzRoleDefinition：已超出角色定义限制。无法创建更多的角色定义。 "的详细信息_，请参阅 [Azure RBAC 故障排除](../../role-based-access-control/troubleshooting.md)。

## <a name="create-a-shared-image-gallery"></a>创建共享映像库

创建库。

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

创建库定义。

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>创建映像

创建 Azure 映像生成器源对象。 请参阅 [在 Azure Marketplace 中查找 WINDOWS VM 映像，并 Azure PowerShell](./cli-ps-findimage.md) 有效的参数值。

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

创建 Azure 映像生成器分发服务器对象。

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

创建 Azure 映像生成器自定义对象。

```azurepowershell-interactive
$ImgCustomParams = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer = New-AzImageBuilderCustomizerObject @ImgCustomParams
```

创建 Azure 映像生成器模板。

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

完成后，将返回一条消息，并在中创建一个图像生成器配置模板 `$imageResourceGroup` 。

若要确定模板创建过程是否成功，可以使用以下示例。

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

在后台，图像生成器还会在您的订阅中创建一个暂存资源组。 此资源组用于生成映像。 它采用以下格式： `IT_<DestinationResourceGroup>_<TemplateName>` 。

> [!WARNING]
> 不要直接删除暂存资源组。 删除映像模板项目，这将导致删除暂存资源组。

如果服务在映像配置模板提交期间报告失败：

- 请参阅 [排查 AZURE VM 映像构建 (AIB) 故障](../linux/image-builder-troubleshoot.md)。
- 重试之前，请使用以下示例删除模板。

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>启动映像生成

将映像配置提交给 VM 映像生成器服务。

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

等待映像生成过程完成。 此步骤最多需要一小时。

如果遇到错误，请查看 [疑难解答 AZURE VM 映像构建 (AIB) 故障](../linux/image-builder-troubleshoot.md)。

## <a name="create-a-vm"></a>创建 VM

将 VM 的登录凭据存储在一个变量中。 密码必须是复杂密码。

```azurepowershell-interactive
$Cred = Get-Credential
```

使用所创建的映像创建 VM。

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customization"></a>验证自定义

使用创建 VM 时设置的用户名和密码创建与 VM 的远程桌面连接。 在 VM 中，打开 PowerShell 并运行， `Get-Content` 如以下示例中所示：

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

应会看到基于映像自定义过程中所创建文件的内容的输出。

```Output
Azure-Image-Builder-Was-Here
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用本文中创建的资源，可以运行以下示例将其删除。

### <a name="delete-the-image-builder-template"></a>删除图像生成器模板

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>删除映像资源组

> [!CAUTION]
> 以下示例删除指定的资源组及其包含的所有资源。
> 如果指定的资源组中存在本文范围外的资源，这些资源也会被删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>后续步骤

若要详细了解本文中使用的 json 文件的组件，请参阅 [图像生成器模板参考](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
