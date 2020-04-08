---
title: 开发安全的 Azure AD Web 应用程序 |微软文档
description: 此简单示例应用实现了安全最佳实践，在 Azure 上开发时可改进应用程序和组织的安全状态。
keywords: na
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 599c4a31840b47294b43c4c4d1f0200b17f04540
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810547"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>为 Azure AD 应用开发安全应用
## <a name="overview"></a>概述

此示例是一个简单的 Azure 活动目录，带有 Web 应用，用于链接到用于在 Azure 上开发应用的安全资源。 该应用程序实现了安全最佳实践，这些最佳实践可帮助改进应用程序和组织在 Azure 上开发应用时的安全状况。

部署脚本设置基础结构。 运行部署脚本后，需要在 Azure 门户中执行一些手动配置，以将组件和服务链接在一起。 此示例面向 Azure 上经验丰富的开发人员，他们在零售行业中工作，并希望使用安全的 Azure 基础结构构建安全的 Azure 活动目录。 


在开发和部署此应用程序时，您将学习如何 
- 创建 Azure 密钥保管库实例，存储并从中检索机密。
- 部署 Azure Web 应用，该应用专用于与前端防火墙访问隔离。 
- 使用使用 OWASP 前 10 个规则集的防火墙创建和配置 Azure 应用程序网关实例。 
- 使用 Azure 服务对传输中和静态的数据进行加密。 
- 设置 Azure 策略和安全中心以评估兼容性。 

开发和部署此应用后，您将设置以下示例 Web 应用以及描述的配置和安全措施。

## <a name="architecture"></a>体系结构
该应用程序是一个典型的n层应用程序，具有三层。 此处显示了集成了监视和密钥管理组件的前端、后端和数据库层：

![应用体系结构](./media/secure-aad-app/architecture.png)

此解决方案使用以下 Azure 服务。 部署体系结构的详细信息位于"部署体系结构"部分。 

体系结构由这些组件组成

- [Azure 应用程序网关](../../application-gateway/index.yml)。 为我们的应用程序体系结构提供网关和防火墙。
- [应用程序见解](../../azure-monitor/app/app-insights-overview.md)。 在多个平台上提供可扩展的应用程序性能管理 （APM） 服务。
- [Azure 密钥保管库](../../key-vault/index.yml)。 存储和加密应用的机密，并管理围绕其创建访问策略。
- [Azure 活动目录](../../active-directory/fundamentals/active-directory-whatis.md)。提供基于云的标识和访问管理服务、登录和访问资源。
- [Azure 域名系统](../../dns/dns-overview.md)。 提供托管域的服务。
- [Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)。 提供扩展应用程序并为服务创建高可用性。
- [Azure Web 应用程序](../../app-service/overview.md)。  提供基于 HTTP 的服务来托管 Web 应用程序。
- [Azure 安全中心](../../security-center/index.yml)。 跨云中的混合工作负载提供高级威胁保护。
- [Azure 策略](../../governance/policy/overview.md). 提供评估资源以不符合分配的策略。

## <a name="threat-model"></a>威胁模型
威胁建模是识别业务和应用程序的潜在安全威胁，然后确保制定适当的缓解计划的过程。

此示例使用[Microsoft 威胁建模工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)为安全示例应用实现威胁建模。 通过绘制组件和数据流图，可以在开发过程的早期识别问题和威胁。 时间和金钱将节省以后使用这个。

下面是示例应用的威胁模型

![威胁模型](./media/secure-aad-app/threat-model.png)

以下屏幕截图中显示了威胁建模工具生成的一些示例威胁和潜在漏洞。 威胁模型概述了暴露的攻击面，并提示开发人员考虑如何缓解问题。

![威胁模型输出](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>先决条件
要启动和运行应用程序，您需要安装这些工具：

- 用于修改和查看应用程序代码的代码编辑器。[可视化工作室代码](https://code.visualstudio.com/)是一个开源选项。
- [开发计算机上的 Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,)
- [Git](https://git-scm.com/)在你的系统上。 Git 用于在本地克隆源代码。
- [jq，](https://stedolan.github.io/jq/)一种以用户友好方式查询JSON的UNIX工具。

您需要 Azure 订阅来部署示例应用的资源。 如果没有 Azure 订阅，则可以[创建一个免费帐户](https://azure.microsoft.com/free/)来测试示例应用。

安装这些工具后，即可在 Azure 上部署应用。

### <a name="implementation-guidance"></a>实施指南
部署脚本是一个脚本，可以分解为四个阶段。 每个阶段部署和配置[体系结构关系图](#architecture)中的 Azure 资源。

四个阶段是

- 部署 Azure 密钥保管库。
- 部署 Azure Web 应用。
- 使用 Web 应用程序防火墙部署应用程序网关。
- 使用已部署的应用配置 Azure AD。

每个阶段都使用以前部署的资源中的配置在前一个阶段构建。

要完成实施步骤，请确保已安装"[先决条件](#prerequisites)"下列出的工具。

#### <a name="deploy-azure-key-vault"></a>部署 Azure 密钥保管库
在本节中，创建和部署用于存储机密和证书的 Azure 密钥保管库实例。

完成部署后，在 Azure 上部署了 Azure 密钥保管库实例。

使用 Powershell 部署 Azure 密钥保管库
 
1. 声明 Azure 密钥保管库的变量。
2. 注册 Azure 密钥保管库提供程序。
3. 为实例创建资源组。
4. 在步骤 3 中创建的资源组中创建 Azure 密钥保管库实例。

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>以下 Azure AD 用户将对密钥保管库具有管理员权限
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>注册 Az 提供程序
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>创建 Azure 密钥保管库实例
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>将管理员策略添加到密钥保管库
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>创建访问策略以允许用户获取和列出加密密钥、证书和机密（如果您知道用户主体名称）：
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

最佳做法是在使用密钥保管库访问资源的应用中对 Azure 资源使用托管标识。 当密钥保管库的访问密钥未存储在代码或配置中时，您的安全状态会提高。

容器中包含根证书。 获取证书的步骤包括

1. 从[证书颁发机构](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)下载证书文件。
2. 解码证书文件：

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
此脚本为应用服务实例创建分配的身份，该标识可与 MSI 一起使用，与 Azure 密钥保管库进行交互，而不会在代码或配置中使用硬编码机密。

转到门户中的 Azure 密钥保管库实例，以授权访问策略选项卡上分配的标识。选择 **"添加新访问策略**"。 在**Select 主体**下，搜索与创建的应用服务实例的名称类似的应用程序名称。
附加到应用程序的服务主体应可见。 选择它并保存访问策略页，如下图所示。

由于应用程序只需要检索密钥，因此在机密选项中选择 **"获取**"权限，允许访问，同时减少授予的权限。

![密钥保管库访问策略](./media/secure-aad-app/kv-access-policy.png)

*创建密钥保管库访问策略*

保存访问策略，然后在 **"访问策略"** 选项卡上保存新更改以更新策略。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>在启用了 Web 应用程序防火墙后部署应用程序网关
在 Web 应用中，不建议您在互联网上直接向外部世界公开服务。
负载平衡和防火墙规则为传入流量提供了更多的安全性和控制，并帮助您对其进行管理。

部署应用程序网关实例

1. 创建资源组以容纳应用程序网关。
2. 预配虚拟网络以附加到网关。
3. 为虚拟网络中的网关创建子网。
4. 预配公共 IP 地址。
5. 预配应用程序网关。
6. 在网关上启用 Web 应用程序防火墙。

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>部署 Azure Web 应用
Azure 应用服务使您能够使用 Python、Ruby、C# 和 Java 等语言构建和托管 Web 应用。 Azure 还支持自定义容器，它允许在 Azure 应用服务平台上运行几乎所有编程语言。

#### <a name="create-an-app-service-plan-in-free-tier"></a>在免费套餐中创建应用服务计划
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>创建 Web 应用
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>在继续之前，请转到自定义域的 Azure 域名系统配置 UI，然后按照 以下https://aka.ms/appservicecustomdns说明为主机名"www"配置 CNAME 记录，并将其指向 Web 应用的默认域名

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>将应用服务计划升级到共享层（自定义域所需的最低要求）
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>向 Web 应用添加自定义域名
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="network"></a>网络
完成部署后，您有一个启用了 Web 应用程序防火墙的应用程序网关。

网关实例公开 HTTPS 端口 443。 此配置可确保我们的应用只能通过 HTTPS 在端口 443 上访问。

阻止未使用的端口和限制攻击面暴露是一种安全最佳做法。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>将网络安全组添加到应用服务实例

应用服务实例可以与虚拟网络集成。 此集成允许使用管理应用传入和传出流量的网络安全组策略配置它们。

1. 要启用此功能，请在 Azure 应用服务实例边栏选项卡上，**在"设置"** 下选择 **"网络**"。 在右侧窗格中，在**VNet 集成**下配置 。

   ![新的虚拟网络集成](./media/secure-web-app/app-vnet-menu.png)

    *应用服务的新虚拟网络集成*
1. 在下一页上，选择 **"添加 VNET（预览）"。**

1. 在下一个菜单上，选择在以`aad-vnet`开始的部署中创建的虚拟网络。 您可以创建新子网或选择现有子网。
   在这种情况下，创建一个新的子网。 将**地址范围**设置为**10.0.3.0/24，** 并命名子**网应用子网**。

   ![应用服务虚拟网络配置](./media/secure-web-app/app-vnet-config.png)

    *应用服务的虚拟网络配置*

现在，您已经启用了虚拟网络集成，您可以将网络安全组添加到我们的应用。

1. 使用搜索框，搜索**网络安全组**。 在结果中选择**网络安全组**。

    ![搜索网络安全组](./media/secure-web-app/nsg-search-menu.png)

    *搜索网络安全组*

2. 在下一个菜单上，选择 **"添加**"。 输入应位于的 NSG**和资源组****的名称**。 此 NSG 将应用于应用程序网关的子网。

    ![创建 NSG](./media/secure-web-app/nsg-create-new.png)

    *创建 NSG*

3. 创建 NSG 后，选择它。 在其边栏选项卡中，在 **"设置"** 下，选择 **"入站安全规则**"。 配置这些设置以允许通过端口 443 进入应用程序网关的连接。

   ![配置 NSG](./media/secure-web-app/nsg-gateway-config.png)

   *配置 NSG*

4. 在网关 NSG 的出站规则中，通过创建以服务标记为目标的规则，添加允许出站连接到应用服务实例的规则`AppService`

   ![添加 NSG 的出站规则](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *添加 NSG 的出站规则*

    添加另一个出站规则，允许网关将出站规则发送到虚拟网络。

   ![添加其他出站规则](./media/secure-web-app/nsg-outbound-vnet.png)

    *添加其他出站规则*

5. 在 NSG 的子网边栏选项卡上，选择 **"关联**"，选择在部署中创建的虚拟网络，然后选择名为**gw-子网的**网关子网 。 NSG 应用于子网。

6. 创建另一个 NSG，如前面的步骤中，此时为应用服务实例。 给它一个名字。 添加端口 443 的入站规则，就像对应用程序网关 NSG 所做的那样。

   如果在应用服务环境实例上部署了应用服务实例（此应用不是这种情况，则可以通过打开应用服务 NSG 的入站安全组上的端口 454-455 来添加入站规则以允许 Azure 服务运行状况探测。 配置：

   ![添加 Azure 服务运行状况探测的规则](./media/secure-web-app/nsg-create-healthprobes.png)

    *添加 Azure 服务运行状况探测的规则（仅限应用服务环境）*

要限制攻击面，请修改应用服务网络设置，以仅允许应用程序网关访问应用程序。
要应用设置，请转到应用服务网络选项卡，选择 **"IP 限制"** 选项卡，并创建只允许应用程序网关的 IP 直接访问服务的允许规则。 可以从网关的概述页检索网关的 IP 地址。 在**IP 地址 CIDR**选项卡上，输入此格式的`<GATEWAY_IP_ADDRESS>/32`IP 地址： 。

![仅允许网关](./media/secure-web-app/app-allow-gw-only.png)

*仅允许网关 IP 访问应用服务*

### <a name="azure-domain-name-system"></a>Azure 域名系统 
Azure 域名系统（或 Azure 域名系统）负责将网站或服务名称翻译（或解析）到其 IP 地址。 Azure 域名系统（https://docs.microsoft.com/azure/dns/dns-overview)是使用 Azure 基础结构提供名称解析的域名系统域的托管服务）。 通过在 Azure 中托管域，用户可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理域名系统记录。 Azure 域名系统还支持专用域名系统域。

### <a name="azure-disk-encryption"></a>Azure 磁盘加密
Azure 磁盘加密利用 Windows 的 BitLocker 功能，为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

### <a name="identity-management"></a>身份管理
以下技术提供了在 Azure 环境中管理对持卡人数据的访问的功能
- Azure Active Directory 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 此解决方案的所有用户都在 Azure 活动目录中创建，包括访问 Azure WebApp 的用户。
- Azure 基于角色的访问控制使管理员能够定义细粒度的访问权限，以仅授予用户执行作业所需的访问量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问持卡人数据。 订阅访问仅限于订阅管理员。
- Azure Active Directory Privileged Identity Management 使客户能够最大限度地减少有权访问持卡人数据等特定信息的用户数量。 管理员可以使用 Azure Active Directory Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、实时的管理访问。
- Azure 活动目录标识保护可检测影响组织身份的潜在漏洞，配置自动响应，以检测与组织标识相关的可疑操作，并调查可疑事件以采取适当的措施解决它们。
### <a name="secrets-management"></a>机密管理
此解决方案使用 Azure Key Vault 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure 密钥保管库功能可帮助客户保护和访问此类数据
   - 根据需要配置高级访问权限策略。
   - 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
   - Key Vault 中的所有密钥和机密都有过期日期。
   - Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型是硬件安全模块 （HSM） 保护的 2048 位 RSA 密钥。
   - 使用密钥保管库，您可以加密密钥和机密（如身份验证密钥、存储帐户密钥、数据加密密钥、通过使用受硬件安全模块 （HSM） 保护的密钥，PFX 文件和密码。 
   - 使用基于角色的访问控制 （RBAC） 将权限分配给特定范围内的用户、组和应用程序。     
   - 使用密钥保管库通过自动续订管理 TLS 证书。 
   - Key Vault 的诊断日志已启用，其保留期至少为 365 天。
   - 对密钥进行允许的加密操作时，仅限必需的操作。
### <a name="azure-security-center"></a>Azure 安全中心
借助 Azure 安全中心，客户可在工作负载中集中应用和管理安全策略、限制威胁暴露，以及检测和应对攻击。 此外 
   - Azure 安全中心访问 Azure 服务的现有配置，以提供配置和服务建议，以帮助改进安全状态和保护数据。
   - Azure 安全中心使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 Azure 安全中心有一组预定义的安全警报，这些警报在出现威胁或可疑活动时触发。 客户可以使用 Azure 安全中心的自定义警报规则，根据从环境中收集到的数据定义新的安全警报。
   - Azure 安全中心提供区分优先级的安全警报和事件，让客户更轻松地发现和解决潜在安全问题。 针对检测到的每种威胁生成威胁智能报告，帮助事件响应团队调查和解决威胁。
### <a name="azure-application-gateway"></a>Azure 应用程序网关 
   体系结构使用配置了 Web 应用程序防火墙并启用了 OWASP 规则集的应用程序网关，来降低安全漏洞风险。 其他功能包括
   - 端到端 TLS。
   - 禁用 TLS v1.0 和 v1.1。
   - 启用 TLSv1.2。
   - Web 应用程序防火墙（预防模式）。
   - 具有 OWASP 3.0 规则集的预防模式。
   - 启用诊断日志记录。
   - 自定义运行状况探测。
   - Azure 安全中心和 Azure 顾问提供其他保护和通知。 Azure 安全中心还提供信誉系统。
### <a name="logging-and-auditing"></a>日志记录和审核
Azure 服务广泛记录系统和用户活动以及系统运行状况：
   - 活动日志：[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供对订阅中资源执行的操作的见解。 活动日志可帮助确定操作的发起方、发生的时间和状态。
   - 诊断日志：[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、密钥保管库审核日志以及应用程序网关访问和防火墙日志。 所有诊断日志都将写入到集中式加密 Azure 存储帐户以进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。
### <a name="azure-monitor-logs"></a>Azure Monitor 日志
   这些日志合并在[Azure 监视器日志](https://azure.microsoft.com/services/log-analytics/)中，用于处理、存储和仪表板报告。 收集后，数据在 Log Analytics 工作区内按数据类型整理到不同的表中，这样即可不考虑最初来源而集中分析所有数据。 此外，Azure 安全中心与 Azure 监视器日志集成，允许客户使用 Kusto 查询访问其安全事件数据并将其与其他服务的数据合并。

   以下 Azure[监视解决方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)作为此体系结构的一部分包含在内

   - [活动目录评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：活动目录运行状况检查解决方案在常规时间间隔内评估服务器环境的风险和运行状况，并提供特定于已部署服务器基础结构的建议的优先级列表。
   - [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理运行状况解决方案报告部署的代理数量及其地理分布，以及响应响应的代理数量以及提交操作数据的代理数。
   - [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户所有 Azure 订阅的 Azure 活动日志。
### <a name="azure-monitor"></a>Azure Monitor
   [Azure 监视器](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)通过使组织能够审核、创建警报和存档数据（包括跟踪其 Azure 资源中的 API 调用）帮助用户跟踪性能、维护安全性和识别趋势。
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理服务。 Application Insights 可检测性能异常，客户可以使用它来监视实时 Web 应用程序。 它包含强大的分析工具来帮助客户诊断问题，了解用户在应用中实际执行了哪些操作。 它旨在帮助客户持续提高性能和可用性。

### <a name="azure-key-vault"></a>Azure Key Vault
   为存储密钥的组织创建保管库，并维护操作任务的问责制，如下所示

   - 存储在密钥保管库中的数据包括   
   - Application Insights 密钥
   - 数据存储访问密钥
   - 连接字符串
   - 数据表名
   - 用户凭据
   - 根据需要配置高级访问权限策略
   - 密钥保管库访问策略的定义具有对密钥和机密的最低要求权限
   - Key Vault 中的所有密钥和机密都有过期日期
   - 密钥保管库中的所有密钥都受硬件安全模块 （HSM） [密钥类型 = 硬件安全模块 （HSM） 保护保护       
     2048 位 RSA 密钥*
   - 使用基于角色的访问控制 （RBAC） 授予所有用户/标识所需的最低权限
   - 应用程序共享 Key Vault 的前提是彼此信任且需要在运行时访问相同的机密
   - Key Vault 的诊断日志已启用，其保留期至少为 365 天。
   - 对密钥进行允许的加密操作时，仅限必需的操作

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
   需要通过安全地建立与作为此 PaaS Web 应用程序参考体系结构一部分部署的资源的连接来配置安全的 VPN 隧道或[ExpressRoute。](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

   在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行，并允许客户安全地在客户网络和 Azure 之间的加密链路内"隧道"信息。 站点到站点 VPN 是安全且成熟的技术，各种规模的企业已部署该技术数十年。 此选项使用 IPsec 隧道模式作为加密机制。

   由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了另一个更安全的连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快、延迟时间更短且安全性更高。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

   我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

#### <a name="implement-azure-active-directory-oidc"></a>实现 Azure 活动目录 OIDC

1. 要克隆源代码存储库，请使用此 Git 命令

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>更新重定向 URL
1.  导航回 Azure 门户。 在左侧导航窗格中，选择“Azure Active Directory”服务，然后选择“应用注册”。
2.  在结果的屏幕中，选择 WebApp-OpenIDConnect-DotNet 代码 v2 应用程序。
3.  在"身份验证"选项卡 o 在"重定向 URI"部分中，在组合框中选择 Web 并添加以下重定向 URI。
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o 在"高级设置"部分中，将注销 URL 设置为https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  在"品牌"选项卡 o 将主页 URL 更新到应用服务的地址，例如https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net。
        o 保存配置。
5.  如果应用程序调用 Web api，请确保在项目应用设置.json 上应用必要的更改，以便调用已发布的 API URL 而不是本地主机。
发布示例
    1.  在“应用服务”的“概述”选项卡中，单击“获取发布配置文件”链接下载该配置文件并保存。 还可以使用其他部署机制，如源代码管理。
    2.  切换到可视化工作室，然后转到 WebApp-OpenIDConnect-DotNet 代码 v2 项目。 在“解决方案资源管理器”中右键单击该项目，然后选择“发布”。 在底部栏上单击“导入配置文件”，然后导入先前下载的发布配置文件。
    3.  单击"配置"并在"连接"选项卡中更新目标 URL，使其成为主页 URL 中的 https，例如https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net。 单击“下一步”。
    4.  在“设置”选项卡上，确保未选中“启用组织身份验证”。 单击“保存”。 在主屏幕上单击“发布”。
    5.  Visual Studio 将发布项目，并自动打开浏览器以访问该项目的 URL。 如果看到该项目的默认网页，则发布成功。
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>实现 Azure 活动目录的多重身份验证
   管理员需要确保门户中的订阅帐户受到保护。 订阅容易受到攻击，因为它管理您创建的资源。 为了保护订阅，请在订阅的**Azure 活动目录**选项卡上启用多重身份验证。

   Azure AD 基于应用于符合特定条件的用户或用户组的策略进行操作。
Azure 创建一个默认策略，指定管理员需要双重身份验证才能登录到门户。
启用此策略后，系统可能会提示您注销并重新登录到 Azure 门户。

为管理员登录启用 MFA

   1. 转到 Azure 门户中的**Azure 活动目录**选项卡
   2. 在安全类别下，选择条件访问。 您看到此屏幕

       ![条件访问 - 策略](./media/secure-aad-app/ad-mfa-conditional-add.png)

如果无法创建新策略

   1. 转到**MFA**选项卡。
   2. 选择 Azure AD 高级**免费试用版**链接以订阅免费试用版。

   ![Azure AD 高级免费试用版](./media/secure-aad-app/ad-trial-premium.png)

返回到条件访问屏幕。

   1. 选择新的策略选项卡。
   2. 输入策略名称。
   3. 选择要为其启用 MFA 的用户或组。
   4. 在 **"访问"控件**下，选择"**授予**"选项卡，然后选择 **"需要多重身份验证**"（如果需要，则选择其他设置）。

   ![要求 MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   您可以通过选择屏幕顶部的复选框来启用策略，也可以在 **"条件访问**"选项卡上启用策略。启用策略后，用户需要 MFA 才能登录到门户。

   有一个基线策略，它要求所有 Azure 管理员使用 MFA。 您可以立即在门户中启用它。 启用此策略可能会使当前会话无效，并迫使您再次登录。

   如果未启用基线策略
   1.   为**管理员选择"需要 MFA"。**
   2.   **选择立即使用策略**。

   ![立即选择使用策略](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>使用 Azure 哨兵监视应用和资源

   随着应用程序的增长，很难聚合从资源接收的所有安全信号和指标，并使它们以面向操作的方式有用。

   Azure Sentinel 旨在收集数据、检测可能的威胁类型，并提供安全事件的可见性。
在等待手动干预时，Azure Sentinel 可以依赖预先编写的行动手册来启动警报和事件管理过程。

   示例应用由 Azure Sentinel 可以监视的多个资源组成。
要设置 Azure Sentinel，首先需要创建日志分析工作区，该工作区存储从各种资源收集的所有数据。

创建此工作区

   1. 在 Azure 门户中的搜索框中，搜索**日志分析**。 选择“Log Analytics 工作区”****。

   ![搜索日志分析工作区](./media/secure-aad-app/sentinel-log-analytics.png)

   *搜索日志分析工作区*

   2. 在下一页上，选择 **"添加**"，然后为工作区提供名称、资源组和位置。
   ![创建 Log Analytics 工作区](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *创建日志分析工作区*

   3. 使用搜索框搜索 Azure**哨兵**。

   ![搜索 Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *搜索 Azure 哨兵*

   4. 选择 **"添加**"，然后选择之前创建的日志分析工作区。

   ![添加日志分析工作区](./media/secure-aad-app/sentinel-workspace-add.png)

   *添加日志分析工作区*

   5. 在**Azure 哨兵 - 数据连接器**页上，在**配置**下，选择 **"数据连接器**"。 您将看到一系列 Azure 服务，可以链接到 Azure Sentinel 中的日志分析存储实例进行分析。

   ![日志分析数据连接器](./media/secure-aad-app/sentinel-connectors.png)

      *向 Azure 哨兵添加数据连接器*

   例如，要连接应用程序网关，请执行以下步骤：

   1. 打开 Azure 应用程序网关实例边栏选项卡。
   2. 在“监视”下，选择“诊断设置”********。
   3. 选择“添加诊断设置”。****

   ![添加应用程序网关诊断](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *添加应用程序网关诊断*

   4. 在 **"诊断设置"** 页上，选择您创建的日志分析工作区，然后选择要收集的所有指标并将其发送到 Azure Sentinel。 选择“保存”。 

   ![Azure 哨兵连接器设置](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>成本注意事项
   如果还没有 Azure 帐户，则可以创建一个免费帐户。 转到[免费帐户页面](https://azure.microsoft.com/free/)即可开始，了解可以使用免费 Azure 帐户做什么，并了解哪些产品在 12 个月内是免费的。

   要使用安全功能部署示例应用中的资源，您需要为某些高级功能付费。 随着应用的扩展以及 Azure 提供的免费层和试用版需要升级以满足应用程序要求，您的成本可能会增加。 使用 Azure[定价计算器](https://azure.microsoft.com/pricing/calculator/)估计成本。

## <a name="next-steps"></a>后续步骤
   以下文章可帮助您设计、开发和部署安全应用程序。

- [设计](secure-design.md)
- [开发](secure-develop.md)
- [部署](secure-deploy.md)
