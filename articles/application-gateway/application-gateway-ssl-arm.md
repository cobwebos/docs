---
title: "配置 SSL 卸载 - Azure 应用程序网关 - PowerShell | Microsoft Docs"
description: "本文简要介绍如何使用 Azure 资源管理器创建支持 SSL 卸载的应用程序网关"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 4bcd002a805f1584323f5baf75f791f2fb64201f
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>使用 Azure 资源管理器配置应用程序网关以进行 SSL 卸载

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure 经典 PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

可将 Azure 应用程序网关配置为在网关上终止安全套接字层 (SSL) 会话，以避免 Web 场中出现开销较高的 SSL 解密任务。 SSL 卸载还简化了 Web 应用程序的前端服务器设置与管理。

## <a name="before-you-begin"></a>开始之前

1. 使用 Web 平台安装程序安装最新版本的 Azure PowerShell cmdlet。 可以从[下载页](https://azure.microsoft.com/downloads/)的“Windows PowerShell”部分下载并安装最新版本。
2. 为应用程序网关创建虚拟网络和子网。 请确保没有虚拟机或云部署正在使用子网。 应用程序网关必须单独位于虚拟网络子网中。
3. 必须存在配置为使用应用程序网关的服务器，或者必须在虚拟网络中为其创建终结点，或者必须为其分配公共 IP 地址或虚拟 IP 地址 (VIP)。

## <a name="what-is-required-to-create-an-application-gateway"></a>创建应用程序网关需要什么？

* 后端服务器池：后端服务器的 IP 地址列表。 列出的 IP 地址应属于虚拟网络子网，或者是公共 IP/VIP。
* 后端服务器池设置：每个池都有一些设置，例如端口、协议和基于 Cookie 的相关性。 这些设置绑定到池，并会应用到池中的所有服务器。
* 前端端口：此端口是应用程序网关上打开的公共端口。 流量将抵达此端口，然后重定向到后端服务器之一。
* 侦听器：侦听器具有前端端口、协议（Http 或 Https；这些设置区分大小写）和 SSL 证书名称（如果要配置 SSL 卸载）。
* 规则：规则会绑定侦听器和后端服务器池，并定义当流量抵达特定侦听器时要定向到的后端服务器池。 目前仅支持 *基本* 规则。 *基本* 规则是一种轮循负载分发模式。

**其他配置说明**

对于 SSL 证书配置， **HttpListener** 中的协议应更改为 **Https** （区分大小写）。 需要将“SslCertificate”元素添加到“HttpListener”，其中包含针对 SSL 证书配置的变量值。 前端端口应更新为 443。

启用基于 Cookie 的相关性：可以配置应用程序网关，以确保来自客户端会话的请求始终定向到 Web 场中的同一 VM。 这种情况可通过插入允许网关适当定向流量的会话 Cookie 实现。 要启用基于 Cookie 的相关性，请在 **BackendHttpSettings** 元素中将 **CookieBasedAffinity** 设置为 **Enabled**。

## <a name="create-an-application-gateway"></a>创建应用程序网关

使用 Azure 经典部署模型和使用 Azure 资源管理器的差别在于：创建应用程序网关的顺序和需要配置的项。

通过资源管理器，应用程序网关的所有组件都将单独配置，然后放在一起创建应用程序网关资源。

以下是创建应用程序网关所要执行的步骤：

1. [创建资源管理器的资源组](#create-a-resource-group-for-resource-manager)
2. [创建应用程序网关的虚拟网络、子网和公共 IP](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway)
3. [创建应用程序网关配置对象](#create-an-application-gateway-configuration-object)
4. [创建应用程序网关资源](#create-an-application-gateway-resource)

## <a name="create-a-resource-group-for-resource-manager"></a>创建 Resource Manager 的资源组

确保切换 PowerShell 模式，以便使用 Azure 资源管理器 cmdlet。 有关详细信息，请参阅[将 Windows PowerShell 与 Resource Manager 配合使用](../powershell-azure-resource-manager.md)。

   1. 输入以下命令：

   ```powershell
   Login-AzureRmAccount
   ```

   2. 若要检查帐户的订阅，请输入以下命令：

   ```powershell
   Get-AzureRmSubscription
   ```

   系统会提示用户使用凭据进行身份验证。

   3. 若要选择要使用的 Azure 订阅，请输入以下命令：

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. 运行以下命令来创建资源组。 （若要使用现有资源组，请跳过此步骤。）

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

Azure 资源管理器要求所有资源组指定一个位置。 此设置用作该资源组中的资源的默认位置。 请确保用于创建应用程序网关的所有命令都使用相同的资源组。

在上述示例中，我们创建了名为“appgw-RG”的资源组，位置为“美国西部”。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>为应用程序网关创建虚拟网络和子网

以下示例演示如何使用资源管理器创建虚拟网络：

   1. 输入以下命令：

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   此示例将地址范围 10.0.0.0/24 分配给用于创建虚拟网络的子网变量。

   2. 输入以下命令：

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   此示例将使用前缀 10.0.0.0/16 和子网 10.0.0.0/24，在“美国西部”区域的“appgw-rg”资源组中创建名为“appgwvnet”的虚拟网络。

   3. 输入以下命令：

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   此示例将子网对象分配给变量 $subnet 以供后续步骤使用。

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>创建前端配置的公共 IP 地址

若要创建前端配置的公共 IP 地址，请输入以下命令：

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

此示例在“美国西部”区域的“appgw-rg”资源组中创建公共 IP 资源“publicIP01”。

## <a name="create-an-application-gateway-configuration-object"></a>创建应用程序网关配置对象

   1. 若要创建应用程序网关配置对象，请输入以下命令：

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   此示例创建名为“gatewayIP01”的应用程序网关 IP 配置。 当应用程序网关启动时，它从配置的子网获取 IP 地址，再将网络流量路由到后端 IP 池中的 IP 地址。 请记住，每个实例需要一个 IP 地址。

   2. 输入以下命令：

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   此示例配置名为“pool01”的后端 IP 地址池，IP 地址为“134.170.185.46”、“134.170.188.221”和“134.170.185.50”。 这些值为接收来自前端 IP 终结点的网络流量的 IP 地址。 将上述示例中的 IP 地址替换为 Web 应用程序终结点的 IP 地址。

   3. 输入以下命令：

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   此示例为后端池中负载均衡的网络流量配置应用程序网关设置“poolsetting01”。

   4. 输入以下命令：

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   此示例为公共 IP 终结点配置名为“frontendport01”的前端 IP 端口。

   5. 输入以下命令：

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   此示例配置用于 SSL 连接的证书。 该证书需采用 PFX 格式，并且密码长度必须为 4 到 12 个字符。

   6. 输入以下命令：

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   此示例创建名为“fipconfig01”的前端 IP 配置，并将公共 IP 地址与前端 IP 配置相关联。

   7. 输入以下命令：

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   此示例创建名为“listener01”的侦听器，并将前端端口与前端 IP 配置和证书相关联。

   8. 输入以下命令：

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   此示例创建名为“rule01”的负载均衡器路由规则，用于配置负载均衡器行为。

   9. 输入以下命令：

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   此示例配置应用程序网关的实例大小。

   > [!NOTE]
   > InstanceCount 的默认值为 2，最大值为 10。 GatewaySize 的默认值为 Medium。 可以在 Standard_Small、Standard_Medium 和 Standard_Large 之间进行选择。

   10. 输入以下命令：

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   本步骤定义要在应用程序网关上使用的 SSL 策略。 有关详细信息，请参阅[在应用程序网关上配置 SSL 策略版本和密码套件](application-gateway-configure-ssl-policy-powershell.md)。

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>使用 New-AzureApplicationGateway 创建应用程序网关

输入以下命令：

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

此示例创建包含上述步骤中所有配置项的应用程序网关。 示例中的应用程序网关名为“appgwtest”。

## <a name="get-the-application-gateway-dns-name"></a>获取应用程序网关 DNS 名称

创建网关后，下一步是配置前端以进行通信。 使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，这会造成不便。 若要确保最终用户能够访问应用程序网关，可以使用 CNAME 记录指向应用程序网关的公共终结点。 有关详细信息，请参阅[在 Azure 中配置自定义域名](../cloud-services/cloud-services-custom-domain-name-portal.md)。 

若要获取应用程序网关 DNS 名称，请使用附加到应用程序网关的 PublicIPAddress 元素检索应用程序网关及其关联的 IP/DNS 名称的详细信息。 使用应用程序网关的 DNS 名称来创建 CNAME 记录，使两个 Web 应用程序都指向此 DNS 名称。 不建议使用 A 记录，因为重新启动应用程序网关后 VIP 可能会变化。


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

如果想要将应用程序网关配置为与内部负载均衡器配合使用，请参阅[创建具有内部负载均衡器 (ILB) 的应用程序网关](application-gateway-ilb.md)。

有关负载均衡选项的其他常规信息，请参阅：

* [Azure 负载均衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理器](https://azure.microsoft.com/documentation/services/traffic-manager/)

