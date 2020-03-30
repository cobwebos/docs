---
title: 使用电源外壳启用 Azure DS 域服务 |微软文档
description: 了解如何使用 Azure AD PowerShell 和 Azure PowerShell 配置和启用 Azure 活动目录域服务。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: ee85002aea962dfa675ac6c09a6bfbaeba8e9e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613233"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>使用 PowerShell 启用 Azure Active Directory 域服务

Azure Active Directory 域服务 (Azure AD DS) 提供与 Windows Server Active Directory 完全兼容的托管域服务，例如域加入、组策略、LDAP、Kerberos/NTLM 身份验证。 使用这些域服务就无需自行部署、管理和修补域控制器。 Azure AD DS 与现有的 Azure AD 租户集成。 这种集成可让用户使用其企业凭据登录，而你可以使用现有的组和用户帐户来保护对资源的访问。

本文介绍如何使用 PowerShell 启用 Azure AD DS。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

要完成本文，您需要以下资源：

* 安装和配置 Azure PowerShell。
    * 如果需要，请按照说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](/powershell/azure/install-az-ps)。
    * 请确保使用[连接-AzAccount][Connect-AzAccount] cmdlet 登录到 Azure 订阅。
* 安装和配置 Azure AD 电源外壳。
    * 如果需要，请按照说明[安装 Azure AD PowerShell 模块并连接到 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 请确保使用[连接 AzureAD][Connect-AzureAD] cmdlet 登录到 Azure AD 租户。
* 需要在 Azure AD 目录中拥有“全局管理员”特权才能启用 Azure AD DS。**
* 需要在 Azure 订阅中拥有“参与者”特权才能创建所需的 Azure AD DS 资源。**

## <a name="create-required-azure-ad-resources"></a>创建所需的 Azure AD 资源

Azure AD DS 需要服务主体和 Azure AD 组。 这些资源允许 Azure AD DS 托管域同步数据，并定义哪些用户具有托管域中的管理权限。

首先，为 Azure AD DS 创建 Azure AD 服务主体以进行通信和身份验证。 特定应用程序 ID 使用名为*域控制器服务*，ID为*2565bd9d-da50-47d4-8b85-4c97f669dc36*。 不要更改此应用程序 ID。

使用[New-AzureADService 主体][New-AzureADServicePrincipal]cmdlet 创建 Azure AD 服务主体：

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

现在创建名为*AAD DC 管理员的*Azure AD 组。 然后，向此组添加的用户被授予在 Azure AD DS 托管域上执行管理任务的权限。

使用[新 AzureAD 组][New-AzureADGroup]cmdlet 创建*AAD DC 管理员*组：

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

创建*AAD DC 管理员*组后，使用[Add-AzureAdGroup 成员][Add-AzureADGroupMember]cmdlet 将用户添加到组。 您首先使用[Get-AzureADGroup][Get-AzureADGroup] cmdlet 获取*AAD DC 管理员*组对象 ID，然后使用[Get-AzureADUser][Get-AzureADUser] cmdlet 获取所需的用户的对象 ID。

在下面的示例中，具有 UPN 的`admin@aaddscontoso.onmicrosoft.com`帐户的用户对象 ID。 将此用户帐户替换为要添加到*AAD DC 管理员*组的用户的 UPN：

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>创建支持的 Azure 资源

首先，使用[注册-AzResource提供程序][Register-AzResourceProvider]cmdlet 注册 Azure AD 域服务资源提供程序：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

接下来，使用[新 AzResourceGroup][New-AzResourceGroup] cmdlet 创建资源组。 在下面的示例中，资源组名为*myResourceGroup，* 并在*西部*区域创建。 使用您自己的名称和所需区域：

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

为 Azure AD 域服务创建虚拟网络和子网。 创建了两个子网 - 一个用于*域服务*，另一个用于*工作负载*。 Azure AD DS 部署到专用*域服务*子网中。 不要将其他应用程序或工作负载部署到此子网。 对 VM 的其余部分使用单独的*工作负载*或其他子网。

使用[新阿兹虚拟网络 Subnet Config][New-AzVirtualNetworkSubnetConfig] cmdlet 创建子网，然后使用[新阿兹虚拟网络][New-AzVirtualNetwork]cmdlet 创建虚拟网络。

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>创建 Azure AD DS 托管域

现在，让我们创建一个 Azure AD DS 托管域。 设置 Azure 订阅 ID，然后为托管域提供名称，如*aaddscontoso.com*。 您可以使用[获取-Az 订阅][Get-AzSubscription]cmdlet 获取订阅 ID。

如果选择支持可用性区域的区域，则 Azure AD DS 资源会跨区域分布以实现额外的冗余。

可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。

对于要跨区域分布 Azure AD DS，无需进行任何配置。 Azure 平台会自动处理资源的区域分配。 有关详细信息，请参阅区域可用性，请参阅 Azure[中有哪些可用性区域？][availability-zones]

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

创建资源并将控件返回到 PowerShell 提示符需要几分钟时间。 Azure AD DS 托管域继续在后台预配，最多可能需要一个小时才能完成部署。 在 Azure 门户中，Azure AD DS 托管域的 **"概述"** 页显示整个部署阶段的当前状态。

当 Azure 门户显示 Azure AD DS 托管域已完成预配时，需要完成以下任务：

* 为虚拟网络更新 DNS 设置，以使虚拟机能够找到用于域加入或身份验证的托管域。
    * 要配置 DNS，请在门户中选择 Azure AD DS 托管域。 在 **"概述"** 窗口中，系统会提示您自动配置这些 DNS 设置。
* 如果在支持可用性区域的区域中创建了 Azure AD DS 托管域，请创建网络安全组以限制 Azure AD DS 托管域的虚拟网络中的流量。 创建 Azure 标准负载均衡器，需要放置这些规则。 此网络安全组保护 Azure AD DS，并且托管域正常工作是必需的。
    * 要创建网络安全组和所需规则，请在门户中选择 Azure AD DS 托管域。 在 **"概述"** 窗口中，系统会提示您自动创建和配置网络安全组。
* [将密码同步启用到 Azure AD 域服务](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，以便最终用户可以使用其公司凭据登录到托管域。

## <a name="complete-powershell-script"></a>完整的 PowerShell 脚本

以下完整的 PowerShell 脚本结合了本文中显示的所有任务。 复制脚本并将其保存到具有扩展名`.ps1`的文件。 在本地 PowerShell 控制台或 Azure[云外壳][cloud-shell]中运行脚本。

> [!NOTE]
> 要启用 Azure AD DS，您必须是 Azure AD 租户的全局管理员。 在 Azure 订阅中，您还需要至少*参与者*权限。

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
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

创建资源并将控件返回到 PowerShell 提示符需要几分钟时间。 Azure AD DS 托管域继续在后台预配，最多可能需要一个小时才能完成部署。 在 Azure 门户中，Azure AD DS 托管域的 **"概述"** 页显示整个部署阶段的当前状态。

当 Azure 门户显示 Azure AD DS 托管域已完成预配时，需要完成以下任务：

* 为虚拟网络更新 DNS 设置，以使虚拟机能够找到用于域加入或身份验证的托管域。
    * 要配置 DNS，请在门户中选择 Azure AD DS 托管域。 在 **"概述"** 窗口中，系统会提示您自动配置这些 DNS 设置。
* 如果在支持可用性区域的区域中创建了 Azure AD DS 托管域，请创建网络安全组以限制 Azure AD DS 托管域的虚拟网络中的流量。 创建 Azure 标准负载均衡器，需要放置这些规则。 此网络安全组保护 Azure AD DS，并且托管域正常工作是必需的。
    * 要创建网络安全组和所需规则，请在门户中选择 Azure AD DS 托管域。 在 **"概述"** 窗口中，系统会提示您自动创建和配置网络安全组。
* [将密码同步启用到 Azure AD 域服务](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，以便最终用户可以使用其公司凭据登录到托管域。

## <a name="next-steps"></a>后续步骤

要查看 Azure AD DS 托管域的操作，可以[域加入 Windows VM、][windows-join][配置安全的 LDAP][tutorial-ldaps]和[配置密码哈希同步][tutorial-phs]。

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
