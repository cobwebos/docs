---
title: "创建和管理 Azure 应用程序网关 - PowerShell | Microsoft 文档"
description: "本页提供有关使用 Azure Resource Manager 创建、配置、启动和删除 Azure 应用程序网关的说明"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 5f1713365406764998de505ff62309bab9fa2567
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>使用 Azure Resource Manager 创建、启动或删除应用程序网关

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 经典 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 模板](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Azure 应用程序网关是第 7 层负载均衡器。 它在不同服务器之间提供故障转移和性能路由 HTTP 请求，而不管它们是在云中还是本地。 应用程序网关提供许多应用程序传送控制器 (ADC) 功能，包括 HTTP 负载均衡、基于 Cookie 的会话相关性、安全套接字层 (SSL) 卸载、自定义运行状况探测、多站点支持，以及许多其他功能。 若要查找支持功能的完整列表，请参阅[应用程序网关概述](application-gateway-introduction.md)。

本文指导完成创建、配置、启动和删除应用程序网关的步骤。

> [!IMPORTANT]
> 在使用 Azure 资源之前，请务必了解 Azure 当前使用两种部署模型：Resource Manager 部署模型和经典部署模型。 在使用任何 Azure 资源之前，请确保你了解 [部署模型和工具](../azure-classic-rm.md)。 可以通过单击本文顶部的选项卡来查看不同工具的文档。 本文档介绍如何使用 Azure Resource Manager 创建应用程序网关。 若要使用经典版本，请转到 [使用 PowerShell 创建应用程序网关经典部署](application-gateway-create-gateway.md)。

## <a name="before-you-begin"></a>开始之前

1. 使用 Web 平台安装程序安装最新版本的 Azure PowerShell cmdlet。 可以从[下载页](https://azure.microsoft.com/downloads/)的“Windows PowerShell”部分下载并安装最新版本。
1. 如果有现有的虚拟网络，请选择现有的一个空子网，或者在现有虚拟网络中创建一个子网，专门供应用程序网关使用。 应用程序网关部署到的虚拟网络必须与要部署在应用程序网关后面的资源相同。
1. 必须存在配置为使用应用程序网关的服务器，或者必须在虚拟网络中为其创建终结点，或者必须为其分配公共 IP/VIP。

## <a name="what-is-required-to-create-an-application-gateway"></a>创建应用程序网关需要什么？

* 后端服务器池：后端服务器的 IP 地址、FQDN 或 NIC 的列表。 如果使用了 IP 地址，则这些地址应该属于虚拟网络子网，或者应该是公共 IP/VIP。
* **后端服务器池设置：**每个池具有端口、协议和基于 Cookie 的相关性等设置。 这些设置绑定到池，并会应用到池中的所有服务器。
* 前端端口：此端口是应用程序网关上打开的公共端口。 流量将抵达此端口，并重定向到后端服务器之一。
* 侦听器：侦听器具有前端端口、协议（Http 或 Https，这些值区分大小写）和 SSL 证书名称（如果要配置 SSL 卸载）。
* 规则：规则会绑定侦听器和后端服务器池，并定义当流量抵达特定侦听器时应定向到的后端服务器池。

## <a name="create-a-resource-group-for-resource-manager"></a>创建 Resource Manager 的资源组

确保使用最新版本的 Azure PowerShell。 [将 Windows PowerShell 与 Resource Manager 配合使用](../powershell-azure-resource-manager.md)中提供了详细信息。

1. 登录到 Azure 并输入凭据。

  ```powershell
  Login-AzureRmAccount
  ```

2. 检查该帐户的订阅。

  ```powershell
  Get-AzureRmSubscription
  ```

3. 选择要使用的 Azure 订阅。

  ```powershell
  Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
  ```

4. 创建资源组（如果要使用现有的资源组，请跳过此步骤）。

  ```powershell
  New-AzureRmResourceGroup -Name ContosoRG -Location "West US"
  ```

Azure Resource Manager 要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 请确保用于创建应用程序网关的所有命令都使用相同的资源组。

在上面的示例中，我们在位置“美国东部”中创建了名为“ContosoRG”的资源组。

> [!NOTE]
> 若需为应用程序网关配置自定义探测，请访问：[使用 PowerShell 创建带自定义探测的应用程序网关](application-gateway-create-probe-ps.md)。 有关详细信息，请查看 [custom probes and health monitoring](application-gateway-probe-overview.md) （自定义探测和运行状况监视）。


## <a name="create-the-application-gateway-configuration-objects"></a>创建应用程序网关配置对象

在创建应用程序网关之前，必须设置所有配置项。 以下步骤将创建应用程序网关资源所需的配置项。

```powershell
# Create a subnet and assign the address space of 10.0.0.0/24
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network with the address space of 10.0.0.0/16 and add the subnet
$vnet = New-AzureRmVirtualNetwork -Name ContosoVNET -ResourceGroupName ContosoRG -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the newly created subnet
$subnet=$vnet.Subnets[0]

# Create a public IP address that is used to connect to the application gateway. Application Gateway does not support custom DNS names on public IP addresses.  If a custom name is required for the public endpoint, a CNAME record should be created to point to the automatically generated DNS name for the public IP address.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName ContosoRG -name publicIP01 -location "East US" -AllocationMethod Dynamic

# Create a gateway IP configuration. The gateway picks up an IP addressfrom the configured subnet and routes network traffic to the IP addresses in the backend IP pool. Keep in mind that each instance takes one IP address.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Configure a backend pool with the addresses of your web servers. These backend pool members are all validated to be healthy by probes, whether they are basic probes or custom probes.  Traffic is then routed to them when requests come into the application gateway. Backend pools can be used by multiple rules within the application gateway, which means one backend pool could be used for multiple web applications that reside on the same host.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Configure backend http settings to determine the protocol and port that is used when sending traffic to the backend servers. Cookie-based sessions are also determined by the backend HTTP settings.  If enabled, cookie-based session affinity sends traffic to the same backend as previous requests for each packet.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

# Configure a frontend port that is used to connect to the application gateway through the public IP address
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

# Configure the frontend IP configuration with the public IP address created earlier.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Configure the listener.  The listener is a combination of the front end IP configuration, protocol, and port and is used to receive incoming network traffic. 
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Configure a basic rule that is used to route traffic to the backend servers. The backend pool settings, listener, and backend pool created in the previous steps make up the rule. Based on the criteria defined traffic is routed to the appropriate backend.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU for the application gateway, this determines the size and whether or not WAF is used.
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Create the application gateway
$appgw = New-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

完成时，从附加到应用程序网关的公共 IP 资源中检索应用程序网关的 DNS 和 VIP 详细信息。

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName ContosoRG
```

## <a name="delete-the-application-gateway"></a>删除应用程序网关

下面的示例删除应用程序网关。

```powershell
# Retrieve the application gateway
$gw = Get-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG

# Stops the application gateway
Stop-AzureRmApplicationGateway -ApplicationGateway $gw

# Once the application gateway is in a stopped state, use the `Remove-AzureRmApplicationGateway` cmdlet to remove the service.
Remove-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG -Force
```

> [!NOTE]
> 可以使用 **-force** 开关来禁止显示该删除的确认消息。

若要验证是否已删除服务，可以使用 `Get-AzureRmApplicationGateway` cmdlet。 此步骤不是必需的。

```powershell
Get-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG
```

## <a name="get-application-gateway-dns-name"></a>获取应用程序网关 DNS 名称

创建网关后，下一步是配置前端以进行通信。 使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，这会造成不方便。 若要确保最终用户能够访问应用程序网关，可以使用 CNAME 记录指向应用程序网关的公共终结点。 为此，可使用附加到应用程序网关的 PublicIPAddress 元素检索应用程序网关及其关联的 IP/DNS 名称的详细信息。 这可通过 Azure DNS 或其他 DNS 提供程序完成，方法是创建指向[公共 IP 地址](../dns/dns-custom-domain.md#public-ip-address)的 CNAME 记录。 不建议使用 A 记录，因为重新启动应用程序网关后 VIP 可能会变化。

> [!NOTE]
> 服务启动时，一个 IP 地址会分配到应用程序网关。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName ContosoRG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : ContosoRG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/ContosoAppGateway/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="delete-all-resources"></a>删除所有资源

若要删除在本文中创建的所有资源，请完成以下步骤：

```powershell
Remove-AzureRmResourceGroup -Name ContosoRG
```

## <a name="next-steps"></a>后续步骤

若要配置 SSL 卸载，请访问：[配置应用程序网关以进行 SSL 卸载](application-gateway-ssl.md)。

要将应用程序网关配置为与内部负载均衡器配合使用，请访问：[创建具有内部负载均衡器 (ILB) 的应用程序网关](application-gateway-ilb.md)。

如需负载均衡选项的其他常规信息，请访问：

* [Azure 负载均衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理器](https://azure.microsoft.com/documentation/services/traffic-manager/)

