---
title: "使用 URL 路由规则创建应用程序网关 | Microsoft Docs"
description: "本页介绍了如何使用 URL 路由规则创建和配置 Azure 应用程序网关。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: b1ed7d5693ff7e6730255462411d462694b730e1
ms.contentlocale: zh-cn
ms.lasthandoff: 09/15/2017

---
# <a name="create-an-application-gateway-by-using-path-based-routing"></a>使用基于路径的路由创建应用程序网关

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

借助基于路径的路由，可根据 HTTP 请求的 URL 路径来关联路由。 它会检查是否有路由通向针对应用程序网关中所列 URL 配置的后端池，并将流量发送到定义的后端池。 基于 URL 的路由的常见用法是将不同内容类型的请求负载均衡到不同的后端服务器池。

Azure 应用程序网关有两种类型的规则：基本路由和基于路径的路由。 基本路由为后端池提供轮循机制服务。 除了提供轮循机制服务之外，基于路径的路由还可以使用请求 URL 的路径模式，以选择后端池。

## <a name="scenario"></a>方案

在下面的示例中，应用程序网关使用两个后端服务器池（视频服务器池和图像服务器池），路由 contoso.com 流量。

http://contoso.com/image* 请求会被路由到图像服务器池 (pool1)，http://contoso.com/video* 请求会被路由到视频服务器池 (pool2)。 如果没有匹配的路径模式，将选择默认服务器池 (pool1)。

![URL 路由](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>开始之前

1. 使用 Web 平台安装程序安装最新版本的 Azure PowerShell cmdlet。 可以从[下载页](https://azure.microsoft.com/downloads/)的“Windows PowerShell”部分下载并安装最新版本。
2. 为应用程序网关创建虚拟网络和子网。 请确保没有虚拟机或云部署使用此子网。 应用程序网关必须单独位于虚拟网络子网中。
3. 确保添加到应用程序网关的后端池的服务器务必存在，或者在虚拟网络中有终结点/分配有公共 IP/VIP。

## <a name="requirements-to-create-an-application-gateway"></a>创建应用程序网关所要满足的要求

* 后端服务器池：后端服务器的 IP 地址列表。 列出的 IP 地址要么应属于虚拟网络子网，要么应为公共 IP/VIP。
* **后端服务器池设置**：如端口、协议和基于 Cookie 的相关性。 这些设置绑定到池，并应用于池中的所有服务器。
* **前端端口**：应用程序网关上打开的公共端口。 流量在抵达此端口后重定向到后端服务器之一。
* **侦听器**：侦听器包含前端端口、协议（Http 或 Https，区分大小写）和 SSL 证书名称（若要配置 SSL 卸载的话）。
* **规则**：规则绑定侦听器和后端服务器池，并定义流量在抵达侦听器时应定向到哪个池。

## <a name="create-an-application-gateway"></a>创建应用程序网关

使用 Azure 经典门户与 Azure 资源管理器的区别在于，创建应用程序网关的顺序和需要配置的项。

使用 Resource Manager 时，组成应用程序网关的所有项都将分开配置，然后放在一起创建应用程序网关资源。

若要创建应用程序网关，请按照以下步骤操作：

1. 创建 Resource Manager 的资源组。
2. 创建应用程序网关的虚拟网络、子网和公共 IP。
3. 创建应用程序网关配置对象。
4. 创建应用程序网关资源。

## <a name="create-a-resource-group-for-resource-manager"></a>创建 Resource Manager 的资源组

确保使用最新版本的 Azure PowerShell。 有关详细信息，请参阅[结合使用 Windows PowerShell 与资源管理器](../powershell-azure-resource-manager.md)。

### <a name="step-1"></a>步骤 1

登录 Azure。

```powershell
Login-AzureRmAccount
```

系统会提示用户使用凭据进行身份验证。<BR>

### <a name="step-2"></a>步骤 2

检查该帐户的订阅。

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>步骤 3

选择要使用的 Azure 订阅。 <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>步骤 4

创建资源组。 （若要使用现有资源组，请跳过此步骤。）

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

也可以为应用程序网关的资源组创建标记：

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Azure 资源管理器要求资源组指定一个默认位置，用于此组中的所有资源。 请确保用于创建应用程序网关的所有命令都使用相同的资源组。

上一示例创建了名为“appgw-RG”的资源组，并使用位置“美国西部”。

> [!NOTE]
> 如果需要为应用程序网关配置自定义探测，请转到[使用 PowerShell 创建应用程序网关的自定义探测](application-gateway-create-probe-ps.md)。 有关详细信息，请参阅[应用程序网关运行状况监视概述](application-gateway-probe-overview.md)。
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>为应用程序网关创建虚拟网络和子网

以下示例演示如何使用 Resource Manager 创建虚拟网络。 此示例为应用程序网关创建虚拟网络。 应用程序网关需要有自己的子网。 出于这个原因，为应用程序网关创建的子网小于虚拟网络地址空间。 这样一来，可以在同一虚拟网络中配置其他资源（包括但不限于 Web 服务器）。

### <a name="step-1"></a>步骤 1

将地址范围 10.0.0.0/24 分配给用于创建虚拟网络的子网变量。  这会为应用程序网关创建子网配置对象（将在下一示例中使用）。

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>步骤 2

结合使用前缀 10.0.0.0/16 和子网 10.0.0.0/24，在“美国西部”区域的 appgw-rg 资源组中创建名为 appgwvnet 的虚拟网络。 这将完成配置虚拟网络以及一个子网，可供应用程序网关驻留。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>步骤 3

分配子网变量，以供后续步骤使用。 在后续步骤中，此变量将传递到 `New-AzureRMApplicationGateway` cmdlet。

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>创建前端配置的公共 IP 地址

在 West US 区域的 **appgw-rg** 资源组中创建公共 IP 资源 **publicIP01**。 应用程序网关可以使用公共 IP 地址和/或内部 IP 地址，从而接收负载均衡请求。  此示例仅使用公共 IP 地址。 在下面的示例中，没有配置用于创建公共 IP 地址的 DNS 名称，因为应用程序网关不支持对公共 IP 地址使用自定义 DNS 名称。  如果公共终结点需要使用自定义名称，请创建 CNAME 记录，使之指向针对公共 IP 地址自动生成的 DNS 名称。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

服务启动时，一个 IP 地址会分配到应用程序网关。

## <a name="create-the-application-gateway-configuration"></a>创建应用程序网关配置

必须先设置所有配置项，然后才能创建应用程序网关。 若要创建应用程序网关资源所需的配置项，请按照以下步骤操作。

### <a name="step-1"></a>步骤 1

创建名为 **gatewayIP01** 的应用程序网关 IP 配置。 当应用程序网关启动时，它会先从配置的子网获取 IP 地址，再将流量路由到后端 IP 池中的 IP 地址。 请记住，每个实例需要一个 IP 地址。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>步骤 2

分别配置名为 pool1 和 pool2 的后端 IP 地址池，且 pool1 和 pool2 具有相应的 IP 地址。 这些是托管 Web 应用程序的资源的 IP 地址，而这些 Web 应用程序将受应用程序网关保护。 这些后端池成员全都由探测（不管是基本探测，还是自定义探测）进行验证，运行状况皆正常。 然后，当请求进入应用程序网关时，系统就会将流量路由到这些成员。 后端池可由应用程序网关中的多个规则使用。 也就是说，可以对驻留在同一主机上的多个 Web 应用程序使用一个后端池。

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

在此示例中，两个后端池根据 URL 路径路由流量。 一个池接收来自 URL 路径“/video”的流量，另一个池接收来自路径“/image”的流量。 替换上述 IP 地址，添加自己的应用程序 IP 地址终结点。 

### <a name="step-3"></a>步骤 3

针对后端池中进行了负载均衡的流量配置应用程序网关设置“poolsetting01”和“poolsetting02”。 在本示例中，将为后端池配置不同的后端池设置。 每个后端池都可以有自己的设置。  规则使用后端 HTTP 设置，将流量路由到正确的后端池成员。 此设置决定了用于将流量发送到后端池成员的协议和端口。 基于 Cookie 的会话也由后端 HTTP 设置决定。 如果启用，基于 Cookie 的会话相关性会将流量发送到与前述每个数据包请求相同的后端。

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>步骤 4

使用公共 IP 终结点配置前端 IP。 侦听器使用前端 IP 配置对象，将外向 IP 地址与侦听器相关联。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>步骤 5

配置应用程序网关的前端端口。 侦听器使用前端端口配置对象，定义应用程序网关侦听器侦听哪个端口上的流量。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>步骤 6

针对用于接收传入流量的公共 IP 地址和端口配置侦听器。 下面的示例使用前面配置的前端 IP 配置、前端端口配置和协议（Http 或 Https，区分大小写）配置侦听器。 在此示例中，侦听器侦听端口 80 上的 HTTP 流量，该端口位于此前创建的公共 IP 地址上。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>步骤 7

配置后端池的 URL 规则路径。 这一步配置应用程序网关使用的相对路径，并定义 URL 路径与分配为处理传入流量的后端池之间的映射。

> [!IMPORTANT]
> 每个路径都必须以“/”开头，并且只能以星号结尾。 有效示例包括 /xyz、/xyz* 或 /xyz/*。 馈送到路径匹配程序的字符串在第一个“?”或“#”之后就不会有任何文本，而且也禁止使用这些字符。 

下面的示例创建两个规则：一个用于“/image/”路径，将流量路由到后端 pool1；另一个用于“/video/”路径，将流量路由到后端 pool2。 这些规则可确保将每组 URL 的流量路由到后端。 例如，http://contoso.com/image/figure1.jpg 流量路由到 pool1，而 http://contoso.com/video/example.mp4 流量路由到 pool2。

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

如果路径不符合任何预定义的路径规则，规则路径映射配置也会配置默认的后端地址池。 例如，http://contoso.com/shoppingcart/test.html 流量路由到 pool1，因为它被定义为非匹配流量的默认池。

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>步骤 8

创建规则设置。 此步骤将应用程序网关配置为使用基于 URL 路径的路由。 之前步骤中定义的 `$urlPathMap` 变量现用于创建基于路径的规则。 这一步将规则与侦听器和之前创建的 URL 路径映射相关联。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>步骤 9

配置实例数目和应用程序网关的大小。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-an-application-gateway"></a>创建应用程序网关

创建包含前述步骤中所有配置对象的应用程序网关。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-an-application-gateway-dns-name"></a>获取应用程序网关 DNS 名称

创建网关后，将配置用于通信的前端。 使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，这会造成不方便。 为了确保客户能够访问应用程序网关，可以使用 CNAME 记录，指向应用程序网关的公共终结点。 有关详细信息，请参阅[为 Azure 云服务配置自定义域名](../cloud-services/cloud-services-custom-domain-name-portal.md)。

若要配置前端 IP CNAME 记录，请使用附加到应用程序网关的 PublicIPAddress 元素，检索应用程序网关及其相关 IP/DNS 名称的详细信息。 使用应用程序网关的 DNS 名称创建 CNAME 记录。 不建议使用 A 记录，因为重启应用程序网关后 VIP 可能会发生变化。

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

若要了解安全套接字层 (SSL) 卸载，请参阅[使用 Azure 资源管理器配置应用程序网关以进行 SSL 卸载](application-gateway-ssl-arm.md)。


