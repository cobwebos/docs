---
title: 使用 PowerShell 启用 Azure DS 域服务 | Microsoft Docs
description: 了解如何使用 Azure AD PowerShell 与 Azure PowerShell 来配置和启用 Azure Active Directory 域服务。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 9c2345c93a163464ea735400c9269e2e3fc27ecf
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488102"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>使用 PowerShell 启用 Azure Active Directory 域服务

Azure Active Directory 域服务 (Azure AD DS) 提供与 Windows Server Active Directory 完全兼容的托管域服务，例如域加入、组策略、LDAP、Kerberos/NTLM 身份验证。 使用这些域服务就无需自行部署、管理和修补域控制器。 Azure AD DS 与现有的 Azure AD 租户集成。 这种集成可让用户使用其企业凭据登录，而你可以使用现有的组和用户帐户来保护对资源的访问。

本文介绍如何使用 PowerShell 启用 Azure AD DS。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

若要完成本文，需准备好以下资源：

* 安装和配置 Azure PowerShell。
    * 如果需要，请按照说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](/powershell/azure/install-az-ps)。
    * 确保使用 [Connect-AzAccount][Connect-AzAccount] cmdlet 登录到 Azure 订阅。
* 安装并配置 Azure AD PowerShell。
    * 如果需要，请按照说明[安装 Azure AD PowerShell 模块并连接到 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 确保使用 [Connect-AzureAD][Connect-AzureAD] cmdlet 登录到 Azure AD 租户。
* 需要在 Azure AD 目录中拥有“全局管理员”特权才能启用 Azure AD DS。
* 需要在 Azure 订阅中拥有“参与者”特权才能创建所需的 Azure AD DS 资源。

## <a name="create-required-azure-ad-resources"></a>创建所需的 Azure AD 资源

Azure AD DS 需要一个服务主体和一个 Azure AD 组。 这些资源使 Azure AD DS 托管域能够同步数据，并定义哪些用户在托管域中拥有管理权限。

首先创建一个 Azure AD 服务主体，使 Azure AD DS 能够通信并对自身进行身份验证。 使用名称为“域控制器服务”的特定应用程序 ID 2565bd9d-da50-47d4-8b85-4c97f669dc36。 请不要更改此应用程序 ID。

使用 [Get-AzureADServicePrincipal][New-AzureADServicePrincipal] cmdlet 创建 Azure AD 服务主体：

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

现在，请创建名为“AAD DC 管理员”的 Azure AD 组。 然后，添加到此组的用户会被授予在托管域上执行管理任务的权限。

使用 [Get-AzureADGroup][New-AzureADGroup] cmdlet 创建“AAD DC 管理员”组：

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

创建“AAD DC 管理员”组后，使用 [Add-AzureADGroupMember][Add-AzureADGroupMember] cmdlet 将一个用户添加到该组。 首先使用 [Get-AzureADGroup][Get-AzureADGroup] cmdlet 获取“AAD DC 管理员”组对象 ID，然后使用 [Get-AzureADUser][Get-AzureADUser] cmdlet 获取所需用户的对象 ID。

以下示例显示了 UPN 为 `admin@contoso.onmicrosoft.com` 的帐户的用户对象 ID。 请将此用户帐户替换为要添加到“AAD DC 管理员”组的用户的 UPN：

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>创建支持的 Azure 资源

首先，使用 [Register-AzResourceProvider][Register-AzResourceProvider] cmdlet 注册 Azure AD 域服务资源提供程序：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

接下来，使用 [New-AzResourceGroup][New-AzResourceGroup] cmdlet 创建一个资源组。 以下示例中，资源组名为 myResourceGroup，在 westus 区域中创建。  使用自己的名称和所需区域：

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

为 Azure AD 域服务创建虚拟网络和子网。 创建两个子网 - 一个用于“DomainServices”，另一个用于“Workloads”。 Azure AD DS 将部署到专用的“DomainServices”子网中。 请不要将其他应用程序或工作负载部署到此子网中。 对剩余的 VM 使用单独的“Workloads”子网或其他子网。

使用 [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] cmdlet 创建子网，然后使用 [New-AzVirtualNetwork][New-AzVirtualNetwork] cmdlet 创建虚拟网络。

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-a-managed-domain"></a>创建托管域

现在，让我们创建托管域。 设置 Azure 订阅 ID，然后提供托管域的名称，例如 aaddscontoso.com。 可以使用 [Get-AzSubscription][Get-AzSubscription] cmdlet 获取订阅 ID。

如果选择支持可用性区域的区域，则 Azure AD DS 资源会跨区域分布以实现额外的冗余。

可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的地区中都必须至少有三个单独的区域。

对于要跨区域分布 Azure AD DS，无需进行任何配置。 Azure 平台会自动处理资源的区域分配。 有关详细信息和区域可用性，请参阅[Azure 中的可用性区域是什么？][availability-zones]。

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

创建资源并将控制权返回给 PowerShell 提示符需要花费几分钟时间。 托管域将在后台继续预配，完成部署最长可能需要一小时。 在 Azure 门户中，托管域的“概览”页会显示整个部署阶段的当前状态。

当 Azure 门户显示托管域已完成预配时，需要完成以下任务：

* 为虚拟网络更新 DNS 设置，以使虚拟机能够找到用于域加入或身份验证的托管域。
    * 若要配置 DNS，请在门户中选择你的托管域。 在“概览”窗口中，系统会提示你自动配置这些 DNS 设置。
* 创建网络安全组，以针对托管域限制虚拟网络中的流量。 创建一个要求实施这些规则的 Azure 标准负载均衡器。 此网络安全组会保护 Azure AD DS，是托管域正常运行所需的。
    * 若要创建网络安全组和所需规则，请首先使用 `Install-Script -Name New-AaddsNetworkSecurityGroup` 命令安装 `New-AzureAddsNetworkSecurityGroup` 脚本，然后运行 `New-AaddsNetworkSecurityGroup`。 系统会为你创建托管域所需的规则。
* [启用 Azure AD DS 的密码同步](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，使最终用户能够使用其企业凭据登录到托管域。

## <a name="complete-powershell-script"></a>完整的 PowerShell 脚本

下述完整的 PowerShell 脚本结合了本文中所述的所有任务。 请复制该脚本，并将其保存到扩展名为 `.ps1` 的文件中。 在本地 PowerShell 控制台或 [Azure Cloud Shell][cloud-shell] 中运行该脚本。

> [!NOTE]
> 只有 Azure AD 租户的全局管理员才能启用 Azure AD DS。 此外，该管理员需要在 Azure 订阅中至少拥有“参与者”特权。

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

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
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

创建资源并将控制权返回给 PowerShell 提示符需要花费几分钟时间。 托管域将在后台继续预配，完成部署最长可能需要一小时。 在 Azure 门户中，托管域的“概览”页会显示整个部署阶段的当前状态。

当 Azure 门户显示托管域已完成预配时，需要完成以下任务：

* 为虚拟网络更新 DNS 设置，以使虚拟机能够找到用于域加入或身份验证的托管域。
    * 若要配置 DNS，请在门户中选择你的托管域。 在“概览”窗口中，系统会提示你自动配置这些 DNS 设置。
* 创建网络安全组，以针对托管域限制虚拟网络中的流量。 创建一个要求实施这些规则的 Azure 标准负载均衡器。 此网络安全组会保护 Azure AD DS，是托管域正常运行所需的。
    * 若要创建网络安全组和所需规则，请首先使用 `Install-Script -Name New-AaddsNetworkSecurityGroup` 命令安装 `New-AzureAddsNetworkSecurityGroup` 脚本，然后运行 `New-AaddsNetworkSecurityGroup`。 系统会为你创建托管域所需的规则。
* [启用 Azure AD DS 的密码同步](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，使最终用户能够使用其企业凭据登录到托管域。

## <a name="next-steps"></a>后续步骤

若要查看托管域的运作方式，可[将某个 Windows VM 加入域][windows-join]，[配置安全 LDAP][tutorial-ldaps]，并[配置密码哈希同步][tutorial-phs]。

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
