---
title: Azure Active Directory 域服务：网络安全组配置疑难解答 | Microsoft Docs
description: Azure AD 域服务的 NSG 配置的疑难解答
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ergreenl
ms.openlocfilehash: a2acbed81e323718c7d294d87ebf699c35664d02
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502639"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>排查托管域的无效网络配置问题
本文可帮助你排查和解决导致以下警报消息的与网络相关的配置错误：

## <a name="alert-aadds104-network-error"></a>警报 AADDS104：网络错误
**警报消息：***Microsoft 不能访问此托管域的域控制器。如果虚拟网络上配置的网络安全组 (NSG) 阻止访问托管域，则可能会发生这种情况。另一个可能的原因为，如果有用户定义的路由阻止来自 Internet 的传入流量。*

无效的 NSG 配置是导致 Azure AD 域服务网络错误的最常见原因。 为虚拟网络配置的网络安全组 (NSG) 必须允许访问[特定端口](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)。 如果已阻止这些端口，Microsoft 将无法监视或更新托管域。 此外，Azure AD 目录与托管域之间的同步也会受到影响。 创建 NSG 时，请保持这些端口处于打开状态，以避免服务中断。

### <a name="checking-your-nsg-for-compliance"></a>检查 NSG 的合规性

1. 在 Azure 门户中导航到[网络安全组](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)页面
2. 从表中选择与启用了托管域的子网关联的 NSG。
3. 在左侧面板的“设置”下，单击“入站安全规则”
4. 就地检查规则并识别是哪些规则阻止了对[这些端口](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)的访问
5. 通过删除规则、添加规则来编辑 NSG，或创建一个全新的 NSG，以确保合规性。 [添加规则](#add-a-rule-to-a-network-security-group-using-the-azure-portal)或[创建一个新的合规性 NSG](#create-a-nsg-for-azure-ad-domain-services-using-powershell)的步骤如下所示

## <a name="sample-nsg"></a>示例 NSG
下表描述了一个示例 NSG，它将在允许 Microsoft 监视、管理和更新信息的同时保持托管域的安全。

![示例 NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD 域服务需要从虚拟网络进行不受限制的出站访问。 我们建议不要创建任何其他 NSG 规则来虚拟网络的出站访问。

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>使用 Azure 门户将规则添加到网络安全组
如果不想使用 PowerShell，则可以使用 Azure 门户手动将单个规则添加到 NSG。 若要在网络安全组中创建规则，请完成以下步骤：

1. 在 Azure 门户中导航到[网络安全组](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)页面。
2. 从表中选择与启用了托管域的子网关联的 NSG。
3. 在左侧面板的“设置”下，单击“入站安全规则”或“出站安全规则”。
4. 通过单击“添加”并填写信息来创建规则。 单击“确定”。
5. 通过在规则表中查找已创建的规则来验证规则。


## <a name="create-a-nsg-for-azure-ad-domain-services-using-powershell"></a>使用 PowerShell 为 Azure AD 域服务创建 NSG
此 NSG 将配置为允许 Azure AD 域服务所需的端口的入站流量，同时拒绝任何其他不需要的入站访问。

**先决条件：安装并配置 Azure PowerShell** 请按照说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

>[!TIP]
> 建议使用最新版本的 Azure PowerShell 模块。 如果已安装较旧版本的 Azure PowerShell 模块，请更新到最新版本。
>

按照下列步骤使用 PowerShell 创建一个新的 NSG。
1. 登录到 Azure 订阅。

  ```PowerShell
  # Log in to your Azure subscription.
  Connect-AzureRmAccount
  ```

2. 使用三条规则来创建 NSG。 以下脚本为 NSG 定义了三条允许访问运行 Azure AD 域服务所需端口的规则。 然后，脚本创建一个包含这些规则的新 NSG。 如果虚拟网络中部署的工作负荷需要，请使用相同的格式添加其他规则以允许其他入站流量。

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  #The following two rules are optional and needed only in certain situations.

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
  $SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 636

  # Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule
  ```

3. 最后，将 NSG 与选定的 vnet 和子网相关联。

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>为 Azure AD 域服务创建和应用 NSG 的完整脚本
>[!TIP]
> 建议使用最新版本的 Azure PowerShell 模块。 如果已安装较旧版本的 Azure PowerShell 模块，请更新到最新版本。
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Connect-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# The following two rules are optional and needed only in certain situations.

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
$SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 636

# Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```


## <a name="need-help"></a>需要帮助？
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
