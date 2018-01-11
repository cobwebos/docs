---
title: "配置 Web 应用程序防火墙：Azure 应用程序网关 | Microsoft 文档"
description: "本文将简要介绍如何在现有的或新的应用程序网关上开始使用 Web 应用程序防火墙。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: e8106805d21b325e33fb3ab376db75cd783b9042
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>在新的或现有应用程序网关上配置 Web 应用程序防火墙

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

了解如何创建启用 Web 应用程序防火墙 (WAF) 的应用程序网关。 此外，了解如何将 WAF 添加到现有的应用程序网关。

Azure 应用程序网关中的 WAF 可保护 Web 应用程序，使其免受常见的基于 Web 的攻击威胁，例如 SQL 注入、跨站点脚本攻击和会话劫持。

 应用程序网关是第 7 层负载均衡器。 它在不同服务器之间提供故障转移和性能路由 HTTP 请求，而不管它们是在云中还是本地。 应用程序网关提供了许多应用程序传送控制器 (ADC) 功能：

 * HTTP 负载均衡
 * 基于 Cookie 的会话关联
 * 安全套接字层 (SSL) 卸载
 * 自定义运行状况探测
 * 对多站点功能的支持
 
 若要查找支持功能的完整列表，请参阅[应用程序网关概述](application-gateway-introduction.md)。

本文介绍了如何[将 WAF 添加到现有的应用程序网关](#add-web-application-firewall-to-an-existing-application-gateway)。 它还演示如何[创建使用 WAF 的应用程序网关](#create-an-application-gateway-with-web-application-firewall)。

![方案图像][scenario]

## <a name="waf-configuration-differences"></a>WAF 配置差异

如果已阅读[使用 PowerShell 创建应用程序网关](application-gateway-create-gateway-arm.md)，就能了解创建应用程序网关时需要配置的 SKU 设置。 在应用程序网关上配置 SKU 时，WAF 会提供其他可定义的设置。 不需要对应用程序网关本身进行任何其他更改。

| **设置** | **详细信息**
|---|---|
|**SKU** |没有 WAF 的普通应用程序网关支持 Standard\_Small、Standard\_Medium 和 Standard\_Large 大小。 随着 WAF 的引入，还提供了其他两个 SKU，分别是 WAF\_Medium 和 WAF\_Large。 小型应用程序网关不支持 WAF。|
|**层** | 可用值为“标准”或 **WAF**。 当你使用 WAF 时，必须选择“WAF”。|
|**模式** | 此设置为 WAF 模式。 允许的值为“检测”和“阻止”。 WAF 设置为“检测”模式时，所有威胁都会存储在日志文件中。 在“阻止”模式下，仍然会记录事件，但攻击者会从应用程序网关收到“403 未授权”响应。|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>将 Web 应用程序防火墙添加到现有的应用程序网关

确保使用最新版本的 Azure PowerShell。 有关详细信息，请参阅[将 Windows PowerShell 与资源管理器配合使用](../powershell-azure-resource-manager.md)。

1. 登录到 Azure 帐户。

    ```powershell
    Login-AzureRmAccount
    ```

2. 选择要用于此方案的订阅。

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. 检索你想要在其中添加 WAF 的网关。

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. 配置 WAF SKU。 可用大小为 **WAF\_Large** 和 **WAF\_Medium**。 在使用 WAF 时，层级必须是“WAF”。 在设置 SKU 时，请确认容量。

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. 如以下示例定义，配置 WAF 设置。 对于 FirewallMode，可用值为“阻止”和“检测”。

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. 使用上述步骤中定义的设置更新应用程序网关。

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

此命令使用 WAF 更新应用程序网关。 了解如何查看应用程序网关的日志，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。 由于 WAF 的安全特性，请定期查看日志，以了解 Web 应用程序的安全状态。

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>创建具有 Web 应用程序防火墙的应用程序网关

以下步骤引导完成创建具有 WAF 的应用程序网关的整个过程。

确保使用最新版本的 Azure PowerShell。 有关详细信息，请参阅[将 Windows PowerShell 与资源管理器配合使用](../powershell-azure-resource-manager.md)。

1. 通过运行 `Login-AzureRmAccount` 登录到 Azure。 系统会提示用户使用凭据进行身份验证。

2. 通过运行 `Get-AzureRmSubscription` 检查该帐户的订阅。

3. 选择要使用的 Azure 订阅。

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>创建资源组

为应用程序网关创建资源组。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure 资源管理器要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 请确保用于创建应用程序网关的所有命令都使用相同的资源组。

在上述示例中，我们创建了名为“appgw-RG”的资源组，位置为“美国西部”。

> [!NOTE]
> 如果需要为应用程序网关配置自定义探测，请参阅 [Create an application gateway with custom probes by using PowerShell](application-gateway-create-probe-ps.md)（使用 PowerShell 创建带自定义探测的应用程序网关）。 有关详细信息，请参阅[自定义探测和运行状况监视](application-gateway-probe-overview.md)。

### <a name="configure-a-virtual-network"></a>配置虚拟网络

应用程序网关需要自己的子网。 在此步骤中，将创建一个具有 10.0.0.0/16 地址空间的虚拟网络和两个子网（一个子网用于应用程序网关，另一子网用于后端池成员）。

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>配置公共 IP 地址

若要处理外部请求，应用程序网关需要公共 IP 地址。 此公共 IP 地址不能将 `DomainNameLabel` 定义为由应用程序网关使用。

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>配置应用程序网关

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> 为使用基本 WAF 配置创建的应用程序网关配置 CRS 3.0 以进行保护。

## <a name="get-an-application-gateway-dns-name"></a>获取应用程序网关 DNS 名称

创建网关后，下一步是配置前端以进行通信。 使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，这会造成不便。 若要确保用户能够访问应用程序网关，可以使用 CNAME 记录指向应用程序网关的公共终结点。 有关详细信息，请参阅[配置 Azure 云服务的自定义域名](../cloud-services/cloud-services-custom-domain-name-portal.md)。 

若要配置别名，可使用附加到应用程序网关的 PublicIPAddress 元素检索应用程序网关及其关联的 IP/DNS 名称的详细信息。 使用应用程序网关的 DNS 名称来创建 CNAME 记录，使两个 Web 应用程序都指向此 DNS 名称。 不建议使用 A 记录，因为在重新启动应用程序网关时可能会更改 VIP。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>后续步骤

若要了解如何配置诊断日志记录，以记录通过 WAF 检测到或阻止的事件，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png
