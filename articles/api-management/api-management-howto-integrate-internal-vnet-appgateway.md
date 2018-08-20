---
title: 如何在包含应用程序网关的虚拟网络中使用 Azure API 管理 | Microsoft 文档
description: 了解如何在使用应用程序网关 (WAF) 作为前端的内部虚拟网络中设置和配置 Azure API 管理
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: sasolank
ms.openlocfilehash: ce4fd27c89f529b9c12999689152c3025648d2ce
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40037913"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>在包含应用程序网关的内部 VNET 中集成 API 管理

##<a name="overview"> </a> 概述

可在内部模式下的虚拟网络中配置 API 管理服务，以便只能从该虚拟网络内部访问该服务。 Azure 应用程序网关是一种 PAAS 服务，提供第 7 层负载均衡器。 它充当反向代理服务，并提供 Web 应用程序防火墙 (WAF) 及其他产品/服务。

将内部 VNET 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：

* 使用同时供内部使用者和外部使用者使用的相同 API 管理资源。
* 使用单个 API 管理资源，并向外部使用者提供在 API 管理中定义的一部分 API。
* 提供配套的方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。

## <a name="prerequisites"></a>先决条件

要执行本文中所述的步骤，必须具有：

* 一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* 证书 - pfx 和 cer 用于 API 主机名，pfx 用于开发人员门户主机名。

##<a name="scenario"> </a> 方案

本文介绍如何对内部和外部使用者使用单个 API 管理服务，并使其充当本地和云 API 的单一前端。 另外，它还介绍了如何使用应用程序网关中提供的路由功能，仅公开一部分 API（在示例中以绿色突出显示）供外部使用。

在第一个设置示例中，只能从虚拟网络内部管理所有 API。 内部使用者（以橙色突出显示）可以访问所有内部和外部 API。 流量永远不会外发到 Internet，并且可以通过 Express Route 线路提供较高的性能。

![url 路由](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> 准备工作

* 确保使用最新版本的 Azure PowerShell。 [将 Windows PowerShell 与资源管理器配合使用](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager)中提供了详细信息。

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>在 API 管理与应用程序网关之间创建集成需要做好哪些准备？

* **后端服务器池：** API 管理服务的内部虚拟 IP 地址。
* **后端服务器池设置：** 每个池都有一些设置，例如端口、协议和基于 Cookie 的关联性。 这些设置将应用到池中的所有服务器。
* **前端端口：** 此端口是应用程序网关上打开的公共端口。 抵达此端口的流量将重定向到后端服务器之一。
* **侦听器：** 侦听器具有前端端口、协议（Http 或 Https，这些值区分大小写）和 SSL 证书名称（如果要配置 SSL 卸载）。
* **规则：** 规则将侦听器绑定到后端服务器池。
* **自定义运行状况探测：** 默认情况下，应用程序网关使用基于 IP 地址的探测来判断 BackendAddressPool 中的哪些服务器处于活动状态。 API 管理服务只响应包含正确主机标头的请求，因此默认的探测会失败。 需要定义一个自定义运行状况探测，帮助应用程序网关确定服务处于活动状态，应该转发该请求。
* **自定义域证书**：要从 Internet 访问 API 管理，需要创建从服务主机名到应用程序网关前端 DNS 名称的 CNAME 映射。 这可以确保发送到应用程序网关，并转发到 API 管理的主机名标头和证书是 APIM 可以识别为有效的对象。 在此示例中，我们将使用两个证书 - 用于后端和开发人员门户。  

## <a name="overview-steps"></a> 集成 API 管理和应用程序网关所要执行的步骤

1. 创建 Resource Manager 的资源组。
2. 创建应用程序网关的虚拟网络、子网和公共 IP。 为 API 管理创建另一个子网。
3. 在前面创建的 VNET 子网中创建一个 API 管理服务，并确保使用“内部”模式。
4. 在 API 管理服务中设置自定义域名。
5. 创建应用程序网关配置对象。
6. 创建应用程序网关资源。
7. 创建从应用程序网关公共 DNS 名称到 API 管理代理主机名的 CNAME 映射

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>通过公开应用程序网关向外部公开开发人员门户

在本指南中，我们还将通过应用程序网关向外部用户公开开发人员门户。 创建开发人员门户的侦听器、探测、设置和规则需要其他步骤。 相应步骤中提供了所有详细信息。

> [!WARNING]
> 在通过应用程序网关访问的开发人员门户中的所述设置中，可能会遇到与 AAD 和第三方身份验证相关的问题。

## <a name="create-a-resource-group-for-resource-manager"></a>创建资源管理器的资源组

### <a name="step-1"></a>步骤 1

登录 Azure

```powershell
Login-AzureRmAccount
```

使用凭据进行身份验证。

### <a name="step-2"></a>步骤 2

选择所需的订阅。

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzureRmSubscription -Subscriptionid $subscriptionId | Select-AzureRmSubscription
```

### <a name="step-3"></a>步骤 3

创建资源组（如果要使用现有的资源组，请跳过此步骤）。

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzureRmResourceGroup -Name $resGroupName -Location $location
```

Azure 资源管理器要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 请确保用于创建应用程序网关的所有命令都使用相同的资源组。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>为应用程序网关创建虚拟网络和子网

以下示例演示如何使用 Resource Manager 创建虚拟网络。

### <a name="step-1"></a>步骤 1

将地址范围 10.0.0.0/24 分配到创建虚拟网络时用于应用程序网关的子网变量。

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>步骤 2

将地址范围 10.0.1.0/24 分配到创建虚拟网络时用于 API 管理的子网变量。

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>步骤 3

在美国西部地区的资源组“apim-appGw-RG”中创建名为“appgwvnet”的虚拟网络。 使用前缀 10.0.0.0/16 以及子网 10.0.0.0/24 和 10.0.1.0/24。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>步骤 4

分配一个子网变量供后续步骤使用

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>在 VNET 中创建一个配置为内部模式的 API 管理服务

以下示例演示如何在 VNET 中创建一个配置为仅供内部访问的 API 管理服务。

### <a name="step-1"></a>步骤 1

使用前面创建的子网 $apimsubnetdata 创建一个 API 管理虚拟网络对象。

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location $location -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>步骤 2

在虚拟网络中创建一个 API 管理服务。

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzureRmApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

上述命令成功后，请参阅 [DNS Configuration required to access internal VNET API Management service](api-management-using-with-internal-vnet.md#apim-dns-configuration)（访问内部 VNET API 管理服务所要完成的 DNS 配置）访问该服务。 此步骤可能需要半个多小时。

## <a name="set-up-a-custom-domain-name-in-api-management"></a>在 API 管理中设置自定义域名

### <a name="step-1"></a>步骤 1

上传包含域私钥的证书。 本示例将使用 `api.contoso.net` 和 `portal.contoso.net`  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $gatewayCertPfxPath -PfxPassword $gatewayCertPfxPassword -PassThru
$certPortalUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $portalCertPfxPath -PfxPassword $portalCertPfxPassword -PassThru
```

### <a name="step-2"></a>步骤 2

上传证书后，为代理和门户创建主机名配置对象。  

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname $gatewayHostname
$portalHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certPortalUploadResult.Thumbprint -Hostname $portalHostname
$result = Set-AzureRmApiManagementHostnames -Name $apimServiceName -ResourceGroupName $resGroupName –PortalHostnameConfiguration $portalHostnameConfig -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>创建前端配置的公共 IP 地址

在资源组中，创建公共 IP 资源 publicIP01。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

服务启动时，一个 IP 地址会分配到应用程序网关。

## <a name="create-application-gateway-configuration"></a>创建应用程序网关配置

在创建应用程序网关之前，必须设置所有配置项。 以下步骤将创建应用程序网关资源所需的配置项。

### <a name="step-1"></a>步骤 1

创建名为 **gatewayIP01** 的应用程序网关 IP 配置。 当应用程序网关启动时，它会从配置的子网获取 IP 地址，再将网络流量路由到后端 IP 池中的 IP 地址。 请记住，每个实例需要一个 IP 地址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>步骤 2

配置公共 IP 终结点的前端 IP 端口。 此端口是最终用户连接到的端口。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>步骤 3

使用公共 IP 终结点配置前端 IP。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>步骤 4

为应用程序网关配置用于解密和重新加密所传递流量的证书。

```powershell
$certPwd = ConvertTo-SecureString $gatewayCertPfxPassword -AsPlainText -Force
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortalPwd = ConvertTo-SecureString $portalCertPfxPassword -AsPlainText -Force
$certPortal = New-AzureRmApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>步骤 5

为应用程序网关创建 HTTP 侦听器。 为其分配要使用的前端 IP 配置、端口和 SSL 证书。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>步骤 6

为 API 管理服务的 `ContosoApi` 代理域断点创建自定义探测。 路径 `/status-0123456789abcdef` 是所有 API 管理服务中托管的默认运行状况终结点。 将 `api.contoso.net` 设置为自定义探测主机名，以便使用 SSL 证书保护它。

> [!NOTE]
> 主机名 `contosoapi.azure-api.net` 是在公共 Azure 中创建名为 `contosoapi` 的服务时配置的默认代理主机名。
>

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzureRmApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>步骤 7

上传要在已启用 SSL 的后端池资源上使用的证书。 该证书与你在上述步骤 4 中所提供的证书相同。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>步骤 8

为应用程序网关配置 HTTP 后端设置。 这包括为后端请求设置超时限制，超过该限制将取消请求。 此值不同于探测超时。

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>步骤 9

使用前面创建的 API 管理服务的内部虚拟 IP 地址配置名为 **apimbackend** 的后端 IP 地址池。

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>步骤 10

为应用程序网关创建规则和基本路由。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> 更改 -RuleType 和路由，以限制对开发人员门户的特定页面的访问。

### <a name="step-11"></a>步骤 11

配置实例数目和应用程序网关的大小。 本示例将使用 [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) 来提高 API 管理资源的安全性。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>步骤 12

将 WAF 配置为“防护”模式。

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>创建应用程序网关

创建包含前述步骤中所有配置对象的应用程序网关。

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>创建从 API 管理代理主机名到应用程序网关资源公共 DNS 名称的 CNAME 映射

创建网关后，下一步是配置前端以进行通信。 使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，因此可能不容易使用。

应使用应用程序网关的 DNS 名称来创建 CNAME 记录，使 APIM 代理主机名（例如，上面示例中的 `api.contoso.net`）指向此 DNS 名称。 若要配置前端 IP CNAME 记录，可使用 PublicIPAddress 元素检索应用程序网关及其关联的 IP/DNS 名称的详细信息。 不建议使用 A 记录，因为重新启动网关后 VIP 可能会变化。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

##<a name="summary"> </a> 摘要
VNET 中配置的 Azure API 管理为配置的所有 API 提供单个网关接口，无论这些 API 是托管在本地还是云中。 将应用程序网关与 API 管理集成可以灵活地、有选择性地定义允许在 Internet 上访问哪些特定的 API，以及向 API 管理实例提供 Web 应用程序防火墙作为前端。

##<a name="next-steps"> </a> 后续步骤
* 详细了解 Azure 应用程序网关
  * [应用程序网关概述](../application-gateway/application-gateway-introduction.md)
  * [应用程序网关 Web 应用程序防火墙](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [使用基于路径路由的应用程序网关](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* 详细了解 API 管理和 VNET
  * [只能在 VNET 内使用 API 管理](api-management-using-with-internal-vnet.md)
  * [在 VNET 中使用 API 管理](api-management-using-with-vnet.md)
