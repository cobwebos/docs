---
title: "如何在包含应用程序网关的虚拟网络中使用 Azure API 管理 | Microsoft 文档"
description: "了解如何在使用应用程序网关 (WAF) 作为前端的内部虚拟网络中设置和配置 Azure API 管理"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2017
ms.author: sasolank
translationtype: Human Translation
ms.sourcegitcommit: a87349518f7494dda49e52ed160509a4ffeb7775
ms.openlocfilehash: a58ec5f6d62d2b48d6cf85d997b2deac95310505


---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>在包含应用程序网关的内部 VNET 中集成 API 管理 

##<a name="overview"> </a> 概述
 
可在处于内部模式的虚拟网络中配置 API 管理服务，以便只能从该虚拟网络内部访问该服务。 Azure 应用程序网关是一个 PAAS 服务，提供第&7; 层负载均衡器。 它充当反向代理服务，并提供 Web 应用程序防火墙 (WAF) 及其他产品。

将内部 VNET 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：

* 使用同时供内部使用者和外部使用者使用的相同 API 管理资源。
* 使用单个 API 管理资源，并向外部使用者提供在 API 管理中定义的一部分 API。
* 提供配套的方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。 

##<a name="scenario"> </a> 方案
本文介绍如何对内部和外部使用者使用单个 API 管理服务，并使其充当本地和云 API 的单一前端。 然后，还可以使用应用程序网关中提供的 PathBasedRouting 功能，选择仅公开其中的一部分 API（以绿色突出显示）供外部使用。

在此设置中，只能从虚拟网络内部管理所有 API。 内部使用者（以橙色突出显示）可以访问所有内部和外部 API。 为内部使用者带来的好处是，流量永远不会外发到 Internet，并且 Express Route 线路可以提供较高的速度。

![url 路由](./media/api-management-using-with-vnet/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> 准备工作

1. 使用 Web 平台安装程序安装最新版本的 Azure PowerShell cmdlet。 可以从[下载页](https://azure.microsoft.com/downloads/)的“Windows PowerShell”部分下载并安装最新版本。
2. 需要创建一个虚拟网络，并为 API 管理和应用程序网关创建单独的子网。 
3. 如果想要为虚拟网络创建自定义 DNS 服务器，请在开始部署之前创建，并确保在虚拟网络中的新子网内创建的虚拟机可以解析所有 Azure 服务终结点。

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>在 API 管理与应用程序网关之间创建集成需要做好哪些准备？

* **后端服务器池：**API 管理服务的内部虚拟 IP 地址。
* **后端服务器池设置：** 每个池都有一些设置，例如端口、协议和基于 Cookie 的关联性。 这些设置绑定到池，并会应用到池中的所有服务器。
* **前端端口：** 此端口是应用程序网关上打开的公共端口。 流量将抵达此端口，然后重定向到后端服务器之一。
* **侦听器：** 侦听器具有前端端口、协议（Http 或 Https，这些值区分大小写）和 SSL 证书名称（如果要配置 SSL 卸载）。
* **规则：** 规则将会绑定侦听器和后端服务器池，并定义当流量抵达特定侦听器时应定向到的后端服务器池。
* **自定义运行状况探测：**默认情况下，应用程序网关使用基于 IP 地址的探测来判断 BackendAddressPool 中的哪些服务器处于活动状态。 API 管理服务只响应包含正确主机标头的请求，因此默认的探测将会失败。 我们需要定义自定义运行状况探测来让应用程序网关知道服务处于活动状态，应该转发该请求。
* **自定义域证书：**若要从 Internet 访问 API 管理，需要执行从主机名到应用程序网关前端 DNS 名称的 CNAME 映射。 相同的主机名标头和证书将应用到 API 管理中，以便当请求通过应用程序网关传入时，API 管理可以识别到该请求有效并做出响应。
## <a name="overview-steps"></a> 集成 API 管理和应用程序网关所要执行的步骤 

1. 创建 Resource Manager 的资源组。
2. 创建应用程序网关的虚拟网络、子网和公共 IP。 为 API 管理创建另一个子网。
3. 在前面创建的 VNET 子网中，创建一个处于内部 VNET 模式的 API 管理服务。
4. 使用自定义域名更新该 API 管理服务。
5. 创建应用程序网关配置对象。
6. 创建应用程序网关资源。
7. 执行从 API 管理代理主机名到应用程序网关资源公共 DNS 名称的 CNAME 映射。

## <a name="create-a-resource-group-for-resource-manager"></a>创建资源管理器的资源组

确保使用最新版本的 Azure PowerShell。 [将 Windows PowerShell 与 Resource Manager 配合使用](../powershell-azure-resource-manager.md)中提供了详细信息。

### <a name="step-1"></a>步骤 1

登录 Azure

```powershell
Login-AzureRmAccount
```

系统将提示用户使用凭据进行身份验证。<BR>

### <a name="step-2"></a>步骤 2

检查该帐户的订阅并将它选中。

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>步骤 3

创建资源组（如果要使用现有的资源组，请跳过此步骤）。

```powershell
New-AzureRmResourceGroup -Name apim-appGw-RG -Location "West US"
```
Azure 资源管理器要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 请确保用于创建应用程序网关的所有命令都使用相同的资源组。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>为应用程序网关创建虚拟网络和子网

以下示例演示如何使用 Resource Manager 创建虚拟网络。

### <a name="step-1"></a>步骤 1

将地址范围 10.0.0.0/24 分配到创建虚拟网络时用于应用程序网关的子网变量。

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgateway01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>步骤 2

将地址范围 10.0.1.0/24 分配到创建虚拟网络时用于 API 管理的子网变量。

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name apim01 -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>步骤 3

使用前缀 10.0.0.0/16 以及子网 10.0.0.0/24 和 10.0.1.0/24，在美国西部区域的 **apim-appGw-RG** 资源组中创建名为 **appgwvnet** 的虚拟网络。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName apim-appGw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>步骤 4

分配一个子网变量供后续步骤使用

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-in-internal-vnet-mode"></a>创建处于内部 VNET 模式的 API 管理服务

以下示例演示如何在内部 VNET 中创建 API 管理服务

### <a name="step-1"></a>步骤 1
使用前面创建的子网 $apimsubnetdata 创建一个 API 管理虚拟网络对象。

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>步骤 2
在虚拟网络中创建一个 API 管理服务

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization Contoso -AdminEmail admin@contoso.com -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Premium"
```
上述命令成功后，请参阅 [访问内部 VNET API 管理服务所要完成的 DNS 配置][api-management-using-with-internal-vnet.md#apim-dns-configuration] 访问该服务。

## <a name="update-api-management-service-with-custom-domain-name"></a>使用自定义域名更新 API 管理服务

现在，将自定义域名应用到要从 Internet 访问的 API 管理服务终结点代理。

### <a name="step-1"></a>步骤 1
上载包含私钥的证书，表明你有权在域（例如 `*.contoso.net`）中设置自定义域。 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file>
```

### <a name="step-2"></a>步骤 2
上载证书后，便可以使用主机名 `api.contoso.net` 为代理创建主机名配置对象，因为证书已表明我们对 `*.contoso.net` 域的权限。 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" –PortalHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>创建前端配置的公共 IP 地址

在美国西部区域的 **apim-appGw-RG** 资源组中创建公共 IP 资源 **publicIP01**。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

服务启动时，一个 IP 地址会分配到应用程序网关。

## <a name="create-application-gateway-configuration"></a>创建应用程序网关配置

在创建应用程序网关之前，必须设置所有配置项。 以下步骤将创建应用程序网关资源所需的配置项。

### <a name="step-1"></a>步骤 1

创建名为 **gatewayIP01** 的应用程序网关 IP 配置。 当应用程序网关启动时，它会从配置的子网获取 IP 地址，再将网络流量路由到后端 IP 池中的 IP 地址。 请记住，每个实例需要一个 IP 地址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>步骤 2

配置公共 IP 终结点的前端 IP 端口。 此端口是最终用户连接到的端口。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```
### <a name="step-3"></a>步骤 3

使用公共 IP 终结点配置前端 IP。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>步骤 4

配置应用程序网关的证书。 此证书用于加密和解密应用程序网关上的流量。

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>步骤 5

创建应用程序网关的 HTTP 侦听器。 分配要使用的前端 IP 配置、端口和 SSL 证书。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>步骤 6

现在，让我们针对 API 管理服务的 `ContosoApi` 代理域终结点创建自定义探测。 路径 `/status-0123456789abcdef` 是所有 API 管理服务中托管的默认运行状况终结点。 主机名 `contosoapi.azure-api.net` 是在公共 Azure 中创建服务 `contosoapi` 时配置的默认代理主机名。

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name apimproxyprobe -Protocol Https -HostName "contosoapi.azure-api.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>步骤 7

上传要在已启用 SSL 的后端池资源上使用的证书。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>步骤 8

为后端池中的流量配置应用程序网关设置 **apimPoolSetting**。 此步骤还包括针对应用程序网关请求配置后端池响应超时。 后端响应达到超时限制时，应用程序网关取消请求。 此值与仅适用于探测检查的后端响应的探测超时不同。

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name apimPoolSetting -Port 443 -Protocol Https -CookieBasedAffinity Disabled -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>步骤 9

使用前面创建的 API 管理服务的内部虚拟 IP 地址配置名为 **apimbackend** 的后端 IP 地址池。

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name apimbackend -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>步骤 10
配置后端池的 URL 规则路径。 可在 API 管理中配置多个 API，就像使用 `Echo API (/echo/), Calculator API (/calc/) etc.` 时一样，可以选择在通过 Internet 访问时只允许访问 `Echo API`。 

以下示例针对“/echo/”路径创建一个将流量路由到后端“apimProxyBackendPool”的简单规则。

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting

$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule -DefaultBackendAddressPool $apimProxyBackendPool -DefaultBackendHttpSettings $apimPoolSetting
```

上述步骤可确保只允许针对路径“/echo/”发出的请求通过应用程序网关。 从 Internet 访问时，针对在 API 管理中配置的其他 API 发出的请求将在应用程序网关中引发 404 异常。 

### <a name="step-11"></a>步骤 11

创建规则设置。 此步骤将应用程序网关配置为使用基于 URL 路径的路由。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-12"></a>步骤 12

配置实例数目和应用程序网关的大小。 此处我们将使用 [WAF Sku][../application-gateway/application-gateway-webapplicationfirewall-overview.md] 来提升 API 管理资源的安全性。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

### <a name="step-13"></a>步骤 13

将 WAF 的模式配置为“防护”模式
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>创建应用程序网关

创建包含前述步骤中所有配置对象的应用程序网关。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName apim-appGw-RG -Location "West US" -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-public-dns-name-of-application-gateway-resource"></a>执行从 API 管理代理主机名到应用程序网关资源公共 DNS 名称的 CNAME 映射

创建网关后，下一步是配置前端以进行通信。 使用公共 IP 时，应用程序网关需要动态分配 DNS 名称，该名称不友好。 若要配置前端 IP CNAME 记录，可使用附加到应用程序网关的 PublicIPAddress 元素检索应用程序网关及其关联的 IP/DNS 名称的详细信息。 应使用应用程序网关的 DNS 名称来创建 CNAME 记录，使前面在 `api.contoso.net` 中配置的代理主机名指向此 DNS 名称。 不建议使用 A 记录，因为重新启动应用程序网关后 VIP 可能会变化。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -Name publicIP01
```

##<a name="summary"> </a> 摘要
VNET 中的 Azure API 管理服务提供单个网关接口用于管理对所有 API 的访问，无论这些 API 是托管在本地还是云中。 本文深入介绍了谁可以使用 API 以及如何使用。 将应用程序网关与 API 管理中集成可以更灵活地定义允许在 Internet 上访问哪些 API，以及向 API 管理实例提供 Web 应用程序防火墙作为前端。

##<a name="next-steps"> </a> 后续步骤
* 详细了解 Azure 应用程序网关
  * [应用程序网关概述](../application-gateway/application-gateway-introduction.md)
  * [应用程序网关 Web 应用程序防火墙](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
* 详细了解 VNET 中的 API 管理
  * [在 VNET 中使用 API 管理](api-management-using-with-vnet.md)





<!--HONumber=Feb17_HO1-->


