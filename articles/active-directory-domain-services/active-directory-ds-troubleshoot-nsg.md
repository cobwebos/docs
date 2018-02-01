---
title: "Azure Active Directory 域服务：网络安全组配置疑难解答 | Microsoft Docs"
description: "Azure AD 域服务的 NSG 配置的疑难解答"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Azure AD 域服务 - 网络安全组配置疑难解答



## <a name="aadds104-network-error"></a>AADDS104：网络错误

**警报消息：***Microsoft 不能访问此托管域的域控制器。如果虚拟网络上配置的网络安全组 (NSG) 阻止访问托管域，则可能会发生这种情况。另一个可能的原因为，如果有用户定义的路由阻止来自 Internet 的传入流量。*

Azure AD 域服务网络错误的最常见原因可归结于不正确的 NSG 配置。 若要确保 Microsoft 能服务并维护你的托管域，必须使用网络安全组 (NSG) 来允许访问子网内的[特定端口](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)。 如果这些端口遭阻止，Microsoft 无法访问其所需的资源，这可能会对你的服务造成不利影响。 创建 NSG 时，请保持这些端口处于打开状态，以确保服务不会中断。

## <a name="sample-nsg"></a>示例 NSG
下表描述了一个示例 NSG，它将在允许 Microsoft 监视、管理和更新信息的同时保持托管域的安全。

![示例 NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD 域服务要求不受限制的出站访问。 我们建议不要为 NSG 创建任何其他的出站规则。

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>使用 Azure 门户将规则添加到网络安全组
如果不想使用 PowerShell，则可以使用 Azure 门户手动将单个规则添加到 NSG。 按照以下步骤在网络安全组中创建规则。

1. 在 Azure 门户中导航到[网络安全组](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)页面
2. 从表中选择与域相关联的 NSG。
3. 在左侧导航栏的“设置”下，单击“入站安全规则”或“出站安全规则”。
4. 通过单击“添加”并填写信息来创建规则。 单击“确定”。
5. 通过在规则表中查找已创建的规则来验证规则。


## <a name="create-a-default-nsg-using-powershell"></a>使用 PowerShell 来创建默认 NSG

通过 PowerShell 使用以上步骤可创建新的 NSG，在拒绝任何其他不需要的访问的同时，保持运行 Azure AD 域服务所需的所有端口处于打开状态。


此解决方法需要安装和运行 [Azure AD Powershell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0)。

1. 连接到 Azure AD 目录。

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. 登录到 Azure 订阅。

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. 使用三条规则来创建 NSG。 以下脚本为 NSG 定义了三条允许访问运行 Azure AD 域服务所需端口的规则。 然后，脚本创建一个包含这些规则的新 NSG。 欢迎使用相同的形式来添加你认为合适的其他规则。

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. 最后，脚本将 NSG 与选定的 vnet 和子网相关联。

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>完整脚本

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>此默认 NSG 不锁定对用于安全 LDAP 的端口的访问。 若要了解如何为此端口创建规则，请访问[本文](active-directory-ds-troubleshoot-ldaps.md)。
>

## <a name="contact-us"></a>联系我们
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
