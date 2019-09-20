---
title: 开发 Azure AD Web 应用程序的安全 |Microsoft Docs
description: 这个简单的示例应用程序实现了在 Azure 上进行开发时改进应用程序和组织安全状况的最佳安全方案。
keywords: '\n\n'
services: security
documentationcenter: na
author: fehase
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: v-fehase
ms.openlocfilehash: 87df7824a182e68d849fdf967f96b2974b7e0c16
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148167"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>开发 Azure AD 应用程序的安全应用程序
## <a name="overview"></a>概述

此示例是一个简单的 web 应用 Azure Active Directory，链接到用于在 Azure 上开发应用的安全资源。 当你在 Azure 上开发应用程序时，该应用可实现安全最佳做法，有助于改进应用程序和组织的安全状况。

部署脚本设置基础结构。 运行部署脚本后，需要在 Azure 门户中进行一些手动配置，以将组件和服务链接在一起。 此示例面向 Azure 上的经验丰富的开发人员，这些开发人员在零售行业中工作，并希望使用安全的 Azure 基础结构构建安全的 Azure Active Directory。 


在开发和部署此应用中，你将了解如何 
- 创建 Azure Key Vault 实例，存储并从中检索机密。
- 部署 Azure Web 应用，该应用专用于前端防火墙访问。 
- 使用 OWASP Top 10 规则集的防火墙创建和配置 Azure 应用程序网关实例。 
- 使用 Azure 服务对传输中的数据和静态数据启用加密。 
- 设置 Azure 策略和安全中心来评估 compliancies。 

开发和部署此应用后，你将设置以下示例 web 应用以及所述的配置和安全措施。

## <a name="architecture"></a>体系结构
应用是具有三个级别的典型 n 层应用程序。 集成的前端、后端和数据库层以及集成的管理组件如下所示：

![应用体系结构](./media/secure-aad-app/architecture.png)

此解决方案使用以下 Azure 服务。 部署体系结构部分介绍了部署体系结构的详细信息。 

该体系结构包括以下组件

- [Azure 应用程序网关](../../application-gateway/index.yml)。 为应用程序体系结构提供网关和防火墙。
- [Application Insights](../../azure-monitor/app/app-insights-overview.md)。 在多个平台上提供可扩展的应用程序性能管理（APM）服务。
- [Azure Key Vault](../../key-vault/index.yml)。 存储并加密应用的机密，并管理其周围的访问策略创建。
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)。提供基于云的标识和访问管理服务、登录和访问资源。
- [Azure 域名系统](../../dns/dns-overview.md)。 提供服务以托管域。
- [Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)。 提供缩放应用程序并为服务创建高可用性。
- [Azure Web 应用](../../app-service/overview.md)。  提供基于 HTTP 的服务，用于承载 web 应用程序。
- [Azure 安全中心](../../security-center/index.yml)。 提供针对云中混合工作负荷的高级威胁防护。
- [Azure 策略](../../governance/policy/overview.md)。 提供针对不符合已分配策略的资源进行评估。

## <a name="threat-model"></a>威胁模型
威胁建模是识别业务和应用程序的潜在安全威胁的过程，然后确保适当的缓解计划已准备就绪。

此示例使用[Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)实现安全示例应用的威胁建模。 通过绘制组件和数据流的关系图，你可以在开发过程的早期确定问题和威胁。 稍后将使用此保存时间和金钱。

下面是示例应用的威胁模型

![威胁模型](./media/secure-aad-app/threat-model.png)

下面的屏幕截图显示了威胁建模工具生成的一些示例威胁和潜在漏洞。 威胁模型概述了公开的攻击面，并提示开发人员考虑如何缓解这些问题。

![威胁模型输出](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>先决条件
若要启动并运行应用程序，需要安装以下工具：

- 用于修改和查看应用程序代码的代码编辑器。[Visual Studio Code](https://code.visualstudio.com/)为开源选项。
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,)开发计算机上。
- 系统中的[Git](https://git-scm.com/) 。 Git 用于本地克隆源代码。
- [jq](https://stedolan.github.io/jq/)，一种以用户友好的方式查询 JSON 的 UNIX 工具。

需要一个 Azure 订阅来部署示例应用的资源。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)来测试示例应用。

安装这些工具后，便可以在 Azure 上部署应用了。

### <a name="implementation-guidance"></a>实施指南
部署脚本是一个可以分解为四个阶段的脚本。 每个阶段部署并配置[体系结构关系图](#architecture)中的 Azure 资源。

这四个阶段是

- 部署 Azure Key Vault。
- 部署 Azure Web 应用。
- 部署具有 web 应用程序防火墙的应用程序网关。
- 使用部署的应用配置 Azure AD。

每个阶段都通过使用以前部署的资源中的配置来构建。

若要完成实施步骤，请确保已安装 "[先决条件](#prerequisites)" 下列出的工具。

#### <a name="deploy-azure-key-vault"></a>部署 Azure Key Vault
在本部分中，将创建和部署用于存储机密和证书的 Azure Key Vault 实例。

完成部署后，会在 Azure 上部署一个 Azure Key Vault 实例。

使用 Powershell 部署 Azure Key Vault
 
1. 为 Azure Key Vault 声明变量。
2. 注册 Azure Key Vault 提供程序。
3. 为实例创建资源组。
4. 在步骤3中创建的资源组中创建 Azure Key Vault 实例。

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>以下 Azure AD 用户将具有对 Key Vault 的管理员权限
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>注册 Az Providers
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>创建 Azure Key Vault 实例
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>将管理员策略添加到 Key Vault
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>如果你知道用户主体名称，则创建访问策略以允许用户获取并列出加密密钥、证书和机密：
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

最佳做法是在使用 Key Vault 的应用程序中使用 Azure 资源的托管标识来访问资源。 如果 Key Vault 的访问密钥未存储在代码或配置中，则安全状况会提高。

根证书包含在容器中。 获取证书所采取的步骤如下

1. 从[证书颁发机构](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)下载证书文件。
2. 解码证书文件：

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
此脚本将为应用服务实例创建一个已分配的标识，该标识可与 MSI 一起使用以与 Azure Key Vault 进行交互，而不会在代码或配置中对密码进行硬编码。

请访问门户中的 Azure Key Vault 实例，在 "访问策略" 选项卡上授权分配的标识。选择 "**添加新访问策略**"。 在 "**选择主体**" 下，搜索与创建的应用服务实例的名称相似的应用程序名称。
附加到应用程序的服务主体应可见。 选择它并单击 "保存访问策略" 页，如以下屏幕截图所示。

由于应用程序只需检索密钥，因此请在 "机密" 选项中选择 "**获取**" 权限，允许访问，同时减少所授予的特权。

![密钥保管库访问策略](./media/secure-aad-app/kv-access-policy.png)

*创建 Key Vault 访问策略*

保存访问策略，然后在 "**访问策略**" 选项卡上保存新更改，以更新策略。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>部署启用了 web 应用程序防火墙的应用程序网关
在 web 应用中，不建议直接向 internet 上的外部公开服务。
负载均衡和防火墙规则提供了更高的安全性并控制传入流量，并可帮助你对其进行管理。

部署应用程序网关实例

1. 创建资源组以容纳应用程序网关。
2. 预配要附加到网关的虚拟网络。
3. 为虚拟网络中的网关创建子网。
4. 预配公共 IP 地址。
5. 预配应用程序网关。
6. 在网关上启用 web 应用程序防火墙。

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.0.0/24

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

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.0.0

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 

#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>部署 Azure Web 应用
Azure App Service 使你能够使用 Python、Ruby、 C#和 Java 等语言构建和托管 web 应用。 Azure 还支持自定义容器，它几乎可以在 Azure App Service 平台上运行所有编程语言。

#### <a name="create-an-app-service-plan-in-free-tier"></a>在免费层中创建应用服务计划
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>创建 Web 应用
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>继续操作前，请访问自定义域的 Azure 域名系统配置 UI，并按照中的说明 https://aka.ms/appservicecustomdns 配置主机名 "www" 的 CNAME 记录，并将其指向 web 应用的默认域名

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>将应用服务计划升级到共享层（自定义域需要的最低要求）
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>向 web 应用添加自定义域名
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="network"></a>网络
完成部署后，你将拥有一个启用了 web 应用程序防火墙的应用程序网关。

网关实例对 HTTPS 公开端口443。 此配置可确保只能通过 HTTPS 在端口443上访问我们的应用程序。

最佳安全做法是，阻止未使用的端口并限制攻击面暴露。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>将网络安全组添加到应用服务实例

应用服务实例可以与虚拟网络集成。 此集成允许将它们配置为管理应用的传入和传出流量的网络安全组策略。

1. 若要启用此功能，请在 "Azure 应用服务实例" 边栏选项卡上的 "**设置**" 下选择 "**网络**"。 在右侧窗格中，在 " **VNet 集成**" 下配置。

   ![新的虚拟网络集成](./media/secure-web-app/app-vnet-menu.png)

    *新的应用服务虚拟网络集成*
1. 在下一页上，选择 "**添加 VNET （预览版）** "。

1. 在下一菜单中，选择在以开头`aad-vnet`的部署中创建的虚拟网络。 你可以创建一个新的子网，也可以选择一个现有子网。
   在这种情况下，请创建新的子网。 将**地址范围**设置为**10.0.3.0/24** ，并将子网命名**为子网。**

   ![应用服务虚拟网络配置](./media/secure-web-app/app-vnet-config.png)

    *应用服务的虚拟网络配置*

现在，已启用虚拟网络集成，可以将网络安全组添加到应用。

1. 使用 "搜索" 框，搜索 "**网络安全组**"。 在结果中选择 "**网络安全组**"。

    ![搜索网络安全组](./media/secure-web-app/nsg-search-menu.png)

    *搜索网络安全组*

2. 在下一菜单中，选择 "**添加**"。 输入 NSG 的**名称**及其所在的**资源组**。 此 NSG 将应用到应用程序网关的子网。

    ![创建 NSG](./media/secure-web-app/nsg-create-new.png)

    *创建 NSG*

3. 创建 NSG 后，将其选中。 在其边栏选项卡中的 "**设置**" 下，选择 "**入站安全规则**"。 将这些设置配置为允许通过端口443进入应用程序网关的连接。

   ![配置 NSG](./media/secure-web-app/nsg-gateway-config.png)

   *配置 NSG*

4. 在网关 NSG 的出站规则中，通过创建以服务标记为目标的规则，添加允许与应用服务实例建立出站连接的规则`AppService`

   ![为 NSG 添加出站规则](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *为 NSG 添加出站规则*

    添加另一个出站规则，以允许网关将出站规则发送到虚拟网络。

   ![添加另一个出站规则](./media/secure-web-app/nsg-outbound-vnet.png)

    *添加另一个出站规则*

5. 在 NSG 的 "子网" 边栏选项卡中，选择 "**关联**"，选择在部署中创建的虚拟网络，然后选择名为 " **gw-子**网" 的网关子网。 NSG 将应用于子网。

6. 如前面的步骤所示，创建另一个 NSG，这是应用服务实例的时间。 为其命名。 添加端口443的入站规则，就像对应用程序网关 NSG 执行的操作一样。

   如果在应用服务环境实例上部署了应用服务实例（这对于此应用不是这种情况），则可以通过在应用服务 NSG 的入站安全组中打开端口454-455 来添加入站规则，以允许 Azure 服务运行状况探测。 下面是配置：

   ![为 Azure 服务运行状况探测添加规则](./media/secure-web-app/nsg-create-healthprobes.png)

    *为 Azure 服务运行状况探测添加规则（仅应用服务环境）*

若要限制攻击面，请修改应用服务网络设置，仅允许应用程序网关访问应用程序。
若要应用这些设置，请访问 "应用服务网络" 选项卡，选择 " **IP 限制**" 选项卡，并创建允许仅允许应用程序网关 IP 直接访问服务的允许规则。 可以从其 "概述" 页检索网关的 IP 地址。 在 " **Ip 地址 CIDR** " 选项卡上，按以下格式输入 IP `<GATEWAY_IP_ADDRESS>/32`地址：。

![仅允许网关](./media/secure-web-app/app-allow-gw-only.png)

*仅允许网关 IP 访问应用服务*

### <a name="azure-domain-name-system"></a>Azure 域名系统 
Azure 域名系统或 Azure 域名系统负责将网站或服务名称转换（或解析）为它的 IP 地址。 Azure 域名系统（ https://docs.microsoft.com/azure/dns/dns-overview) 是域名系统域的托管服务，可使用 Azure 基础结构提供名称解析。 通过在 Azure 中托管域，用户可以使用与其他 Azure 服务相同的凭据、Api、工具和计费来管理域名系统记录。 Azure 域名系统还支持专用域名系统域。

### <a name="azure-disk-encryption"></a>Azure 磁盘加密
Azure 磁盘加密利用 Windows 的 BitLocker 功能为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

### <a name="identity-management"></a>身份管理
以下技术提供了在 Azure 环境中管理对持卡人数据的访问权限的功能
- Azure Active Directory 是 Microsoft 的基于云的多租户目录和标识管理服务。 在 Azure Active Directory 中创建此解决方案的所有用户，包括访问 Azure WebApp 的用户。
- Azure 基于角色的访问控制允许管理员定义细化访问权限，以仅授予用户执行其作业所需的访问权限量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问持卡人数据。 订阅访问仅限于订阅管理员。
- Azure Active Directory Privileged Identity Management 使客户能够最大程度地减少有权访问某些信息（例如持卡人数据）的用户的数量。 管理员可以使用 Azure Active Directory Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、实时的管理访问。
- Azure Active Directory Identity Protection 检测影响组织标识的潜在漏洞，配置对检测到的与组织标识相关的可疑操作的自动响应，并调查可疑事件以采取适当的措施来解决问题。
### <a name="secrets-management"></a>机密管理
解决方案使用 Azure Key Vault 来管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure Key Vault 功能可帮助客户保护和访问此类数据
   - 根据需要配置高级访问权限策略。
   - 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
   - Key Vault 中的所有密钥和机密都有过期日期。
   - Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型为硬件安全模块（HSM）保护的2048位 RSA 密钥。
   - 通过 Key Vault，可以加密密钥和机密（例如身份验证密钥、存储帐户密钥、数据加密密钥）。PFX 文件和密码），方法是使用受硬件安全模块（Hsm）保护的密钥。 
   - 使用基于角色的访问控制（RBAC）将权限分配给特定范围内的用户、组和应用程序。     
   - 使用 Key Vault 通过自动续订管理 TLS 证书。 
   - Key Vault 的诊断日志已启用，其保留期至少为 365 天。
   - 对密钥进行允许的加密操作时，仅限必需的操作。
### <a name="azure-security-center"></a>Azure 安全中心
使用 Azure 安全中心，客户可以跨工作负荷集中应用和管理安全策略、限制对威胁的暴露以及检测和响应攻击。 附加 
   - Azure 安全中心可以访问 Azure 服务的现有配置，以提供配置和服务建议，以帮助改进安全状况和保护数据。
   - Azure 安全中心使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 Azure 安全中心包含一组预定义的安全警报，在发生威胁或可疑活动时触发。 Azure 安全中心的自定义警报规则允许客户根据已从环境中收集的数据定义新的安全警报。
   - Azure 安全中心提供区分优先级的安全警报和事件，让客户更轻松地发现和解决潜在安全问题。 为每个检测到的威胁生成威胁情报报表，以帮助事件响应团队调查和修正威胁。
### <a name="azure-application-gateway"></a>Azure 应用程序网关 
   体系结构使用配置了 Web 应用程序防火墙并启用了 OWASP 规则集的应用程序网关，来降低安全漏洞风险。 其他功能包括
   - 端到端 SSL。
   - 禁用 TLS 1.0 版和1.1 版。
   - 启用 Tlsv1.1 1.2。
   - Web 应用程序防火墙（防护模式）。
   - 带有 OWASP 3.0 规则集的保护模式。
   - 启用诊断日志记录。
   - 自定义运行状况探测。
   - Azure 安全中心和 Azure 顾问提供额外的保护和通知。 Azure 安全中心还提供信誉系统。
### <a name="logging-and-auditing"></a>日志记录和审核
Azure 服务广泛记录系统和用户活动以及系统运行状况：
   - 活动日志：[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
   - 诊断日志：[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、Key Vault 审核日志、应用程序网关访问和防火墙日志。 所有诊断日志都将写入到集中式加密 Azure 存储帐户以进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。
### <a name="azure-monitor-logs"></a>Azure Monitor 日志
   这些日志合并到[Azure Monitor 日志](https://azure.microsoft.com/services/log-analytics/)中，以便进行处理、存储和仪表板报告。 收集后，数据在 Log Analytics 工作区内按数据类型整理到不同的表中，这样即可不考虑最初来源而集中分析所有数据。 此外，Azure 安全中心与 Azure Monitor 日志集成，使客户可以使用 Kusto 查询访问其安全事件数据，并将其与其他服务中的数据合并。

   以下 Azure[监视解决方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)包括在此体系结构中

   - [Active Directory 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：Active Directory 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并且提供特定于部署的服务器基础结构的优先建议列表。
   - [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理运行状况解决方案报告部署了多少代理及其地理分布，以及有多少代理无响应，以及正在提交操作数据的代理数。
   - [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。
### <a name="azure-monitor"></a>Azure Monitor
   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)通过使组织能够审核、创建警报和存档数据（包括在其 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全和确定趋势。
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理服务。 Application Insights 可检测性能异常，客户可以使用它来监视实时 Web 应用程序。 它包含强大的分析工具来帮助客户诊断问题，了解用户在应用中实际执行了哪些操作。 它旨在帮助客户持续提高性能和可用性。

### <a name="azure-key-vault"></a>Azure Key Vault
   为组织创建保管库，以便在其中存储密钥，并为如下操作任务维护责任

   - Key Vault 中存储的数据包括   
   - Application Insights 密钥
   - 数据存储访问密钥
   - 连接字符串
   - 数据表名称
   - 用户凭据
   - 根据需要配置高级访问权限策略
   - 使用密钥和机密所需的最小权限定义 Key Vault 访问策略
   - Key Vault 中的所有密钥和机密都有过期日期
   - Key Vault 中的所有密钥都受硬件安全模块（HSM） [密钥类型 = 硬件安全模块（HSM）保护       
     2048位 RSA 密钥]
   - 使用基于角色的访问控制（RBAC）向所有用户/标识授予最低要求的权限
   - 应用程序共享 Key Vault 的前提是彼此信任且需要在运行时访问相同的机密
   - Key Vault 的诊断日志已启用，其保留期至少为 365 天。
   - 对密钥进行允许的加密操作时，仅限必需的操作

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
   需要配置安全的 VPN 隧道或[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ，以安全地建立与作为此 PaaS web 应用程序参考体系结构一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

   在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行，并允许客户在客户的网络与 Azure 之间的加密链接内安全 "隧道" 信息。 站点到站点 VPN 是安全且成熟的技术，各种规模的企业已部署该技术数十年。 IPsec 隧道模式作为加密机制在此选项中使用。

   由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了另一个更安全的连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快、延迟时间更短且安全性更高。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

   我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

#### <a name="implement-azure-active-directory-oidc"></a>实现 Azure Active Directory OIDC

1. 若要克隆源代码存储库，请使用此 Git 命令

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>更新重定向 Url
1.  向后导航到 Azure 门户。 在左侧导航窗格中，选择 "Azure Active Directory" 服务，然后选择 "应用注册"。
2.  在生成的屏幕中，选择 WebApp-OpenIDConnect-DotNet 应用程序。
3.  在 "重定向 Uri" 部分的 "身份验证" 选项卡中，在组合框中选择 "Web"，并添加以下重定向 Uri。
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o 在 "高级设置" 部分中，将 "注销 URL" 设置为 https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  例如 https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net ，在 "品牌" 选项卡中，将主页 URL 更新为应用服务的地址。
        o 保存配置。
5.  如果应用程序调用 web api，请确保对项目 appsettings 应用必要的更改，使其调用已发布的 API URL 而不是 localhost。
发布示例
    1.  在应用服务的 "概览" 选项卡中，通过单击 "获取发布配置文件" 链接并保存来下载发布配置文件。 还可以使用其他部署机制，如源代码管理。
    2.  切换到 Visual Studio 并转到 WebApp-OpenIDConnect-DotNet 项目。 右键单击 "解决方案资源管理器中的项目，然后选择" 发布 "。 单击底部栏上的 "导入配置文件"，然后导入之前下载的发布配置文件。
    3.  单击 "配置"，然后在 "连接" 选项卡中更新目标 URL，使其成为主页 url 中的 https，例如 https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net 。 单击“下一步”。
    4.  在 "设置" 选项卡上，确保未选中 "启用组织身份验证"。 单击“保存”。 在主屏幕上单击 "发布"。
    5.  Visual Studio 将发布项目，并自动打开浏览器并将浏览器打开到项目的 URL。 如果看到项目的默认网页，则发布已成功。
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>为 Azure Active Directory 实施多重身份验证
   管理员需要确保门户中的订阅帐户受到保护。 由于该订阅管理创建的资源，因此它容易受到攻击。 若要保护订阅，请在订阅的 " **Azure Active Directory** " 选项卡上启用多重身份验证。

   Azure AD 根据应用于符合特定条件的用户或用户组的策略来运行。
Azure 会创建默认策略，指定管理员需要双重身份验证才能登录到门户。
启用此策略后，系统可能会提示你注销并重新登录到 Azure 门户。

为管理员登录启用 MFA

   1. 中转到 Azure 门户中的 " **Azure Active Directory** " 选项卡
   2. 在 "安全" 类别下，选择 "条件访问"。 你会看到此屏幕

       ![条件性访问-策略](./media/secure-aad-app/ad-mfa-conditional-add.png)

如果无法创建新策略

   1. 中转到 " **MFA** " 选项卡。
   2. 选择 Azure AD Premium**免费试用版**链接，订阅免费试用版。

   ![Azure AD Premium 免费试用版](./media/secure-aad-app/ad-trial-premium.png)

返回到条件访问屏幕。

   1. 选择 "新建策略" 选项卡。
   2. 输入策略名称。
   3. 选择要为其启用 MFA 的用户或组。
   4. 在 "**访问控制**" 下，选择 "**授权**" 选项卡，然后选择 "**需要多重身份验证**" （以及其他设置，如果需要）。

   ![要求 MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   您可以通过选择屏幕顶部的复选框来启用该策略，也可以在 "**条件访问**" 选项卡上执行此操作。启用策略后，用户需要通过 MFA 登录到门户。

   需要为所有 Azure 管理员提供 MFA 的基准策略。 可以在门户中立即启用它。 启用此策略可能会使当前会话失效，并强制你再次登录。

   如果未启用基线策略
   1.   选择 "**需要对管理员进行 MFA**"。
   2.   选择 "**立即使用策略**"。

   ![立即选择使用策略](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>使用 Azure Sentinel 监视应用和资源

   随着应用程序的增长，很难聚合从资源接收的所有安全信号和指标，并使它们在面向操作的方法中有用。

   Azure Sentinel 旨在收集数据、检测可能的威胁类型，并提供安全事件的可见性。
在等待手动干预的同时，Azure Sentinel 可以依赖预编写的行动手册来启动警报和事件管理流程。

   该示例应用由 Azure Sentinel 可监视的多个资源组成。
若要设置 Azure Sentinel，首先需要创建一个 Log Analytics 的工作区，用于存储从各种资源收集的所有数据。

创建此工作区

   1. 在 Azure 门户的 "搜索" 框中，搜索**Log Analytics**。 选择“Log Analytics 工作区”。

   ![搜索 Log Analytics 工作区](./media/secure-aad-app/sentinel-log-analytics.png)

   *搜索 Log Analytics 工作区*

   2. 在下一页上，选择 "**添加**"，然后提供工作区的名称、资源组和位置。
   ![创建 Log Analytics 工作区](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *创建 Log Analytics 工作区*

   3. 使用 "搜索" 框搜索**Azure Sentinel**。

   ![搜索 Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *搜索 Azure Sentinel*

   4. 选择 "**添加**"，然后选择之前创建的 "Log Analytics" 工作区。

   ![添加 Log Analytics 工作区](./media/secure-aad-app/sentinel-workspace-add.png)

   *添加 Log Analytics 工作区*

   5. 在 " **Azure Sentinel-数据连接器**" 页的 "**配置**" 下，选择 "**数据连接器**"。 你将看到 Azure 服务的数组，你可以链接到 Log Analytics 存储实例，以便在 Azure Sentinel 中进行分析。

   ![Log Analytics 数据连接器](./media/secure-aad-app/sentinel-connectors.png)

      *将数据连接器添加到 Azure Sentinel*

   例如，若要连接应用程序网关，请执行以下步骤：

   1. 打开 "Azure 应用程序网关实例" 边栏选项卡。
   2. 在“监视”下，选择“诊断设置”。
   3. 选择 "**添加诊断设置**"。

   ![添加应用程序网关诊断](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *添加应用程序网关诊断*

   4. 在 "**诊断设置**" 页上，选择你创建的 "Log Analytics" 工作区，然后选择要收集并发送到 Azure Sentinel 的所有指标。 选择“保存”。

   ![Azure Sentinel 连接器设置](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>成本注意事项
   如果还没有 Azure 帐户，可以创建一个免费帐户。 转到[免费帐户页](https://azure.microsoft.com/free/)开始使用免费的 Azure 帐户，并了解12个月免费购买哪些产品。

   若要在包含安全功能的示例应用程序中部署资源，需要为一些高级功能付费。 随着应用规模和 Azure 提供的免费层和试用版的升级，你的成本可能会增加。 使用 Azure[定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。

## <a name="next-steps"></a>后续步骤
   以下文章可帮助你设计、开发和部署安全的应用程序。

- [设计](secure-design.md)
- [开发](secure-develop.md)
- [部署](secure-deploy.md)
