---
title: "使用 PowerShell 启用 Azure Active Directory 域服务 | Microsoft 文档"
description: "使用 PowerShell 启用 Azure Active Directory 域服务"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: maheshu
ms.openlocfilehash: 667e68ad444386a5fe29f9909042fdfa7ca66581
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>使用 PowerShell 启用 Azure Active Directory 域服务
本文介绍如何使用 PowerShell 来启用 Azure Active Directory (AD) 域服务。

## <a name="task-1-install-the-required-powershell-modules"></a>任务 1：安装所需的 PowerShell 模块

### <a name="install-and-configure-azure-ad-powershell"></a>安装和配置 Azure AD PowerShell
请按照本文中的说明[安装 Azure AD PowerShell 模块并连接到 Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

### <a name="install-and-configure-azure-powershell"></a>安装和配置 Azure PowerShell
请按照文章中的说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>任务 2：在 Azure AD 目录中创建所需的服务主体
键入以下 PowerShell 命令，以在 Azure AD 目录中创建 Azure AD 域服务所需的服务主体。
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>任务 3：创建并配置“AAD DC 管理员”组
下一个任务是创建管理员组，用于委托在托管域上的管理任务。
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

现在，已创建了组，然后向该组添加几个用户。
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId -RefObjectId $UserObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>任务 4：注册 Azure AD 域服务资源提供程序
键入以下 PowerShell 命令，以注册 Azure AD 域服务的资源提供程序：
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>任务 5：创建资源组
键入以下 PowerShell 命令，以创建一个资源组：
```powershell
$ResourceGroupName = "ContosoAaddsRg"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location westus
```

可以在此资源组中创建虚拟网络和 Azure AD 域服务托管域。


## <a name="task-6-create-and-configure-the-virtual-network"></a>任务 6：创建并配置虚拟网络
现在，创建在其中可启用 Azure AD 域服务的虚拟网络。 确保为 Azure AD 域服务创建一个专用子网。 请不要将工作负载 VM 部署到此专用子网。

键入以下 PowerShell 命令，为 Azure AD 域服务创建一个具有专用子网的虚拟网络。

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>任务 7：预配 Azure AD 域服务托管域
键入以下 PowerShell 命令，为你的目录启用 Azure AD 域服务：

```powershell
# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location "westus" `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> 在预配托管域后，请不要忘记执行其他配置步骤。
> 预配托管域后，仍需完成以下任务：
> * 为虚拟网络[更新 DNS 设置](active-directory-ds-getting-started-dns.md)，以使虚拟机能够找到用于域加入或身份验证的托管域。
* [启用 Azure AD 域服务的密码同步](active-directory-ds-getting-started-password-sync.md)，以使最终用户能够使用其企业凭据登录到托管域。
>


## <a name="powershell-script"></a>PowerShell 脚本
本文所列的用于执行所有任务的 PowerShell 脚本如下所示。 复制脚本并将其保存到扩展名为“.ps1”的文件。 在 PowerShell 中执行该脚本，或使用 PowerShell 集成脚本环境(ISE)。

> [!NOTE]
> 运行此脚本所需的权限：若要启用 Azure AD 域服务，需要是 Azure AD 目录的全局管理员。 此外，需要至少具有可在其中启用 Azure AD 域服务的虚拟网络上的“参与者”权限。
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Login-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> 在预配托管域后，请不要忘记执行其他配置步骤。
> 预配托管域后，仍需完成以下任务：
> * 为虚拟网络更新 DNS 设置，以使虚拟机能够找到用于域加入或身份验证的托管域。
* 启用 Azure AD 域服务的密码同步，以使最终用户能够使用其企业凭据登录到托管域。
>

## <a name="next-steps"></a>后续步骤
创建托管域后，执行以下配置任务，以使用托管域：

* [更新虚拟网络的 DNS 服务器设置，以指向托管域](active-directory-ds-getting-started-dns.md)
* [启用托管域的密码同步](active-directory-ds-getting-started-password-sync.md)
