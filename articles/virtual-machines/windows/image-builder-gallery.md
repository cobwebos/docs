---
title: 将 Azure 映像生成器用于 Windows VM 的映像库（预览）
description: 使用 Azure 映像生成器和 Azure PowerShell 创建 Azure 共享库映像版本。
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 96603d27f53161c72c9e81bb7b84fdc6ab5d1ca2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320058"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>预览版：创建 Windows 映像并将其分发到共享映像库 

本文介绍如何使用 Azure 映像生成器和 Azure PowerShell，来在[共享映像库](shared-image-galleries.md)中创建映像版本，然后全局分发此映像。 也可以使用 [Azure CLI](../linux/image-builder-gallery.md) 来实现此目的。

我们将使用一个 .json 模板来配置映像。 我们将使用的 .json 文件位于：[armTemplateWinSIG](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json)。 我们将下载并编辑此模板的本地版本，因此本文是使用本地 PowerShell 会话撰写的。

此模板使用 [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) 作为模板的 `distribute` 部分的值，以便将映像分发到共享映像库。

Azure 映像生成器会自动运行 sysprep 来将映像通用化，这是一个通用的 sysprep 命令，如有必要你可以[重写](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully)它。 

请注意层自定义的次数。 最多可以对单个 Windows 映像运行 Sysprep 命令 8 次。 运行 Sysprep 8 次后，必须重新创建 Windows 映像。 有关详细信息，请参阅[有关 Sysprep 可运行次数的限制](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)。 

> [!IMPORTANT]
> Azure 映像生成器目前提供公开预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>注册功能
若要在预览版期间使用 Azure 映像生成器，需要注册新功能。

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

检查功能注册的状态。

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

等到 `RegistrationState` 成为 `Registered`，然后再进入下一步。

检查提供程序注册情况。 请确保每个都返回 `Registered`。

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

如果未返回 `Registered`，请使用以下命令来注册提供程序：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>创建变量

我们将重复使用某些信息，因此我们将创建一些变量来存储这些信息。 将变量（如 `username` 和 `vmpassword`）的值替换为自己的信息。

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Shared Image Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>创建用户分配的标识，并在资源组上设置权限
映像生成器将使用提供的[用户标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)，来将映像注入到 Azure 共享映像库 (SIG)。 在此示例中，你将创建一个 Azure 角色定义，其中包含将映像分发到 SIG 的精细操作。 然后将此角色定义分配给用户标识。

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>为标识分配权限以分发映像

此命令将下载 Azure 角色定义模板，并使用前面指定的参数更新模板。

```powershell
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-shared-image-gallery"></a>创建共享映像库

若要将映像生成器用于共享映像库，需要现有的映像库和映像定义。 映像生成器无法创建映像库和映像定义。

如果还没有可使用的库和映像定义，请先进行创建。 首先创建映像库。

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>下载并配置模板

下载 .json 模板，并使用你的变量对它进行配置。

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>创建映像版本

模板必须提交给服务，此操作会下载任何依赖项目（如脚本），并将其存储在带有“IT_”前缀的暂存资源组中。

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -apiversion "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

需要对模板调用“运行”，以生成映像。

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

创建映像并将其复制到这两个区域，这可能需要一段时间。 等待此部分完成后继续创建 VM。

若要了解用于自动获取映像生成状态的选项，请参阅 GitHub 上此模板的[自述文件](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query)。


## <a name="create-the-vm"></a>创建 VM

从 Azure 映像生成器创建的映像版本创建 VM。

获取所创建的映像版本。
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

在此映像复制到的第二个区域中创建 VM。

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>验证自定义
使用创建 VM 时设置的用户名和密码创建与 VM 的远程桌面连接。 在 VM 中，打开命令提示符并键入以下内容：

```console
dir c:\
```

应该会看到一个名为 `buildActions` 的目录，该目录是在映像自定义期间创建的。


## <a name="clean-up-resources"></a>清理资源
如果现在想要尝试重新自定义映像版本，以创建同一映像的新版本，请跳过此步骤，并继续学习[使用 Azure 映像生成器创建其他映像版本](image-builder-gallery-update-image-version.md)。


这样会删除已创建的映像以及所有其他资源文件。 删除这些资源前，请确保已完成此部署。

首先删除资源组模板，否则 AIB 使用的暂存资源组 (IT_) 将无法清除。

获取映像模板的 ResourceID。 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

删除映像模板。

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

删除角色分配

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

删除定义

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

删除标识

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

删除资源组。

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

若要了解如何更新已创建的映像版本，请参阅[使用 Azure 映像生成器创建其他映像版本](image-builder-gallery-update-image-version.md)。
