---
title: "配置 Web 应用程序防火墙 - Azure 应用程序网关 | Microsoft Docs"
description: "本文提供有关如何在现有的或新的应用程序网关上开始使用 Web 应用程序防火墙的指南。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: bcff92a362c7f8ad1c69b93af07d77e0d03b6a92


---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>在新的或现有的应用程序网关上配置 Web 应用程序防火墙

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-web-application-firewall-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-web-application-firewall-powershell.md)

Azure 应用程序网关中的 Web 应用程序防火墙 (WAF) 可保护 Web 应用程序，使其免受常见 Web 攻击的威胁，例如 SQL 注入、跨站点脚本攻击和会话劫持。

Azure 应用程序网关是第&7; 层负载均衡器。 它在不同服务器之间提供故障转移和性能路由 HTTP 请求，而不管它们是在云中还是本地。 应用程序网关提供许多应用程序传送控制器 (ADC) 功能，包括 HTTP 负载平衡、基于 cookie 的会话相关性、安全套接字层 (SSL) 卸载、自定义运行状况探测、多站点支持，以及许多其他功能。 若要查找支持的功能的完整列表，请参阅“应用程序网关概述”

以下文章说明如何[将 Web 应用程序防火墙添加到现有的应用程序网关](#add-web-application-firewall-to-an-existing-application-gateway)以及如何[创建使用 Web 应用程序防火墙的应用程序网关](#create-an-application-gateway-with-web-application-firewall)。

![方案图像][scenario]

## <a name="waf-configuration-differences"></a>WAF 配置差异

如果已阅读了[使用 PowerShell 创建应用程序网关](application-gateway-create-gateway-arm.md)，就能了解创建应用程序网关时需要配置的 SKU 设置。 在应用程序网关上配置 SKU 时，WAF 会提供其他可定义的设置。 不需要对应用程序网关本身进行任何其他更改。

**SKU** - 没有 WAF 的普通应用程序网关支持 **Standard\_Small**、**Standard\_Medium** 和 **Standard\_Large** 大小。 随着 WAF 的引入，还提供了其他两个 SKU，分别是 **WAF\_Medium** 和 **WAF\_Large**。 小型应用程序网关不支持 WAF。

**层** - 可用值为**标准**或 **WAF**。 使用 Web 应用程序防火墙时，必须选择 **WAF**。

**模式** - 此设置是 WAF 的模式。 允许的值为**检测**和**阻止**。 WAF 设置为检测模式时，所有威胁都会存储在日志文件中。 在阻止模式下，仍然会记录事件，但攻击者会从应用程序网关收到“403 未授权”响应。

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>将 Web 应用程序防火墙添加到现有的应用程序网关

确保使用最新版本的 Azure PowerShell。 [将 Windows PowerShell 与 Resource Manager 配合使用](../powershell-azure-resource-manager.md)中提供了详细信息。

### <a name="step-1"></a>步骤 1

登录 Azure 帐户。

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>步骤 2

选择要用于此方案的订阅。

```powershell
Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>步骤 3

检索要将 Web 应用程序防火墙添加到的网关。

```powershell
$gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
```

### <a name="step-4"></a>步骤 4

配置 Web 应用程序防火墙 SKU。 可用大小为 **WAF\_Large** 和 **WAF\_Medium**。 使用 Web 应用程序防火墙时，层必须是 **WAF**；设置 sku 时，必须确认容量。

```powershell
$gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
```

### <a name="step-5"></a>步骤 5

如以下示例定义，配置 WAF 设置：

对于 **WafMode** 设置，可用值为“阻止”和“检测”。

```powershell
$gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
```

### <a name="step-6"></a>步骤 6

使用上述步骤中定义的设置更新应用程序网关。

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

此命令使用 Web 应用程序防火墙更新应用程序网关。 建议查看[应用程序网关诊断](application-gateway-diagnostics.md)，以了解如何查看应用程序网关的日志。 由于 WAF 的安全特性，需要定期查看日志，以了解 Web 应用程序的安全状态。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>创建具有 Web 应用程序防火墙的应用程序网关

以下步骤引导用户从头到尾完成创建具有 Web 应用程序防火墙的应用程序网关的整个过程。

确保使用最新版本的 Azure PowerShell。 [将 Windows PowerShell 与 Resource Manager 配合使用](../powershell-azure-resource-manager.md)中提供了详细信息。

### <a name="step-1"></a>步骤 1

登录 Azure

```powershell
Login-AzureRmAccount
```

系统将提示用户使用凭据进行身份验证。

### <a name="step-2"></a>步骤 2

检查该帐户的订阅。

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>步骤 3

选择要使用的 Azure 订阅。

```powershell
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-4"></a>步骤 4

创建资源组（如果要使用现有的资源组，请跳过此步骤）。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure 资源管理器要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 请确保用于创建应用程序网关的所有命令都使用相同的资源组。

在上述示例中，我们创建了名为“appgw-RG”的资源组，位置为“美国西部”。

> [!NOTE]
> 如果你需要为应用程序网关配置自定义探测，请参阅 [Create an application gateway with custom probes by using PowerShell](application-gateway-create-probe-ps.md)（使用 PowerShell 创建带自定义探测的应用程序网关）。 有关详细信息，请查看 [custom probes and health monitoring](application-gateway-probe-overview.md) （自定义探测和运行状况监视）。

### <a name="step-5"></a>步骤 5

分配要用于应用程序网关本身的子网地址范围。

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> 应用程序的子网应至少具有 28 个掩码位。 此值会让应用程序网关实例的子网中留出 10 个可用地址。 若使用较小的子网，可能无法在未来添加更多的应用程序网关实例。


### <a name="step-6"></a>步骤 6

分配要用于后端地址池的地址范围。

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-7"></a>步骤 7

使用在[创建资源组](#create-the-resource-group)步骤中所创建的资源组中的上述子网创建虚拟网络

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-8"></a>步骤 8

检索要用于以下步骤的虚拟网络资源和子网资源：

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

### <a name="step-9"></a>步骤 9

创建要用于应用程序网关的公共 IP 资源。 此公共 IP 地址会用于以下步骤之一：

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> 应用程序网关不支持使用通过定义的域标签创建的公共 IP 地址。 仅支持具有动态创建的域标签的公共 IP 地址。 如果需要应用程序网关具有友好的 DNS 名称，建议使用 CNAME 记录作为别名。


### <a name="step-10"></a>步骤 10

在创建应用程序网关之前，必须设置所有配置项。 以下步骤将创建应用程序网关资源所需的配置项。

创建应用程序网关 IP 配置，此设置配置应用程序网关使用的子网。 当应用程序网关启动时，它从配置的子网获取 IP 地址，再将网络流量路由到后端 IP 池中的 IP 地址。 请记住，每个实例需要一个 IP 地址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-11"></a>步骤 11

使用后端 Web 服务器的 IP 地址配置后端 IP 地址池。 这些 IP 地址将接收来自前端 IP 终结点的网络流量。 替换以下 IP 地址，添加自己的应用程序 IP 地址终结点。

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

### <a name="step-12"></a>步骤 12

上传要在已启用 SSL 的后端池资源上使用的证书。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-13"></a>步骤 13

配置应用程序网关后端 http 设置。 将上述步骤中上传的证书分配给 http 设置。

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-14"></a>步骤 14

配置公共 IP 终结点的前端 IP 端口。 此端口是最终用户连接到的端口。

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-15"></a>步骤 15

创建前端 IP 配置，此设置将专用或公共 IP 地址映射到应用程序网关的前端。 以下步骤将上述步骤中的公共 IP 地址与前端 IP 配置关联。

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-16"></a>步骤 16

配置应用程序网关的证书。 此证书用于加密和解密应用程序网关上的流量。

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-17"></a>步骤 17

创建应用程序网关的 HTTP 侦听器。 分配要使用的前端 IP 配置、端口和 SSL 证书。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

### <a name="step-18"></a>步骤 18

创建配置负载均衡器行为的负载均衡器路由规则。 在此示例中，创建基本轮循机制规则。

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-19"></a>步骤 19

配置应用程序网关的实例大小。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

> [!NOTE]
> 可以选择 **WAF\_Medium** 或 **WAF\_Large**，使用 WAF 时的层始终是 **WAF**。 容量是介于 1 和 10 之间的任意数字。

### <a name="step-20"></a>步骤 20

配置 WAF 的模式，可接受的值为**阻止**和**检测**。

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="step-21"></a>步骤 21

创建包含前述步骤中所有配置项的应用程序网关。 示例中的应用程序网关名为“appgwtest”。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

## <a name="get-application-gateway-dns-name"></a>获取应用程序网关 DNS 名称

创建网关后，下一步是配置前端以进行通信。 使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，这会造成不方便。 若要确保最终用户能够访问应用程序网关，可以使用 CNAME 记录指向应用程序网关的公共终结点。 [在 Azure 中配置自定义域名](../cloud-services/cloud-services-custom-domain-name-portal.md)。 为此，可使用附加到应用程序网关的 PublicIPAddress 元素检索应用程序网关及其关联的 IP/DNS 名称的详细信息。 应使用应用程序网关的 DNS 名称来创建 CNAME 记录，使两个 Web 应用程序都指向此 DNS 名称。 不建议使用 A 记录，因为重新启动应用程序网关后 VIP 可能会变化。

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

若要了解如何配置诊断日志记录，以及如何记录通过 Web 应用程序防火墙检测到或阻止的事件，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png



<!--HONumber=Jan17_HO4-->


