---
title: 排查 Azure 应用程序网关错误的网关 (502) 错误
description: 了解如何排查应用程序网关 502 错误
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: amsriva
ms.openlocfilehash: 2a1c7e480e896da6852949c9d765d17290e4e9ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697165"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>排查应用程序网关中的网关无效错误

了解如何排查使用 Azure 应用程序网关时收到的错误的网关 (502) 错误。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概述

配置应用程序网关之后，你可能会看到错误之一是"服务器错误：502 - Web 服务器在作为网关或代理服务器时收到了无效响应”。 对于以下主要原因可能会发生此错误：

* NSG、 UDR 或自定义 DNS 阻止对后端池成员的访问。
* 后端 Vm 或虚拟机规模集的实例未响应默认运行状况探测。
* 自定义运行状况探测的配置无效或不正确。
* Azure 应用程序网关的[后端池未配置或为空](#empty-backendaddresspool)。
* [虚拟机规模集](#unhealthy-instances-in-backendaddresspool)中没有正常运行的 VM 或实例。
* 用户请求出现[请求超时或连接问题](#request-time-out)。

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>网络安全组、用户定义的路由或自定义 DNS 问题

### <a name="cause"></a>原因

如果由于 NSG、 UDR 或自定义 DNS 阻止到后端的访问，应用程序网关实例无法访问后端池。 这会导致探测失败，从而导致 502 错误。

NSG/UDR 可能是在应用程序网关子网或子网中存在其中部署应用程序 Vm。

同样，在 VNet 中的自定义 DNS 存在可能还会导致问题。 FQDN 用于后端池成员可能无法正确解析由用户配置 DNS 服务器的 vnet。

### <a name="solution"></a>解决方案

通过执行以下步骤验证 NSG、UDR 和 DNS 配置：

* 检查与应用程序网关子网的 Nsg 关联。 请确保不会阻止与后端的通信。
* 检查与应用程序网关子网的 UDR 关联。 请确保 UDR 没有将定向离后端子网的流量。 例如，检查路由到网络虚拟设备或默认路由播发到通过 ExpressRoute/VPN 的应用程序网关子网。

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* 检查包含后端 VM 的有效 NSG 和路由

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* 检查 VNet 中是否存在自定义 DNS。 可以通过查看输出中的 VNet 属性的详细信息来检查 DNS。

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
如果存在，请确保 DNS 服务器可以正确解析后端池成员的 FQDN。

## <a name="problems-with-default-health-probe"></a>默认运行状况探测出现问题

### <a name="cause"></a>原因

502 错误也可以是默认运行状况探测无法访问后端 Vm 的频繁指示器。

设置应用程序网关实例时，会自动配置到每个 BackendAddressPool 使用 BackendHttpSetting 的属性的默认运行状况探测。 无需用户输入即可设置此探测。 具体而言，当配置负载均衡规则，BackendHttpSetting 与 BackendAddressPool 之间建立关联。 默认探测配置为每个这些关联，应用程序网关在 BackendHttpSetting 元素中指定的端口上，与 BackendAddressPool 中启动每个实例的定期运行状况检查连接。 

下表列出了与默认运行状况探测关联的值：

| 探测属性 | 值 | 描述 |
| --- | --- | --- |
| 探测 URL |`http://127.0.0.1/` |URL 路径 |
| 时间间隔 |30 |探测间隔（秒） |
| 超时 |30 |探测超时（秒） |
| 不正常阈值 |3 |探测重试计数。 连续探测失败计数达到不正常阈值后，将后端服务器标记为故障。 |

### <a name="solution"></a>解决方案

* 确定默认站点已配置且正在侦听 127.0.0.1。
* 如果 BackendHttpSetting 指定的端口不是 80，则应将默认站点配置为侦听指定的端口。
* 对 `http://127.0.0.1:port` 的调用应返回 HTTP 结果代码 200。 这应在 30 秒超时期限内返回。
* 确保配置的端口已打开，并且没有任何防火墙规则或 Azure 网络安全组，即阻止上配置的端口的传入或传出流量。
* 如果 Azure 经典 Vm 或云服务中使用同一个 FQDN 或公共 IP，请确保相应[终结点](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json)打开。
* 如果 VM 通过 Azure 资源管理器配置，并在其中部署应用程序网关，VNet 的外部[网络安全组](../virtual-network/security-overview.md)必须配置为允许所需的端口上的访问。

## <a name="problems-with-custom-health-probe"></a>自定义运行状况探测出现问题

### <a name="cause"></a>原因

自定义运行状况探测能够对默认探测行为提供更大的弹性。 当使用自定义探测时，可以配置探测间隔、 URL、 要测试的路径和后端池将实例标记为不正常之前可接受的失败的响应次数。

添加以下附加属性：

| 探测属性 | 描述 |
| --- | --- |
| 名称 |探测的名称。 此名称用于在后端 HTTP 设置中引用探测。 |
| Protocol |用于发送探测的协议。 探测使用后端 HTTP 设置中定义的协议 |
| 主机 |用于发送探测的主机名。 仅当应用程序网关上配置多站点时才适用。 这与 VM 主机名不同。 |
| 路径 |探测的相对路径。 有效路径以“/”开头。 将探测发送到 \<protocol\>://\<host\>:\<port\>\<path\> |
| Interval |探测间隔（秒）。 这是每两次连续探测之间的时间间隔。 |
| 超时 |探测超时（秒）。 如果此超时期间内未收到有效响应，则会将探测标记为失败。 |
| 不正常阈值 |探测重试计数。 连续探测失败计数达到不正常阈值后，将后端服务器标记为故障。 |

### <a name="solution"></a>解决方案

根据上表验证是否已正确配置自定义运行状况探测。 除了上述故障排除步骤以外，另请确保符合以下要求：

* 确保已根据[指南](application-gateway-create-probe-ps.md)正确指定了探测。
* 如果应用程序网关配置为单一站点中，默认情况下，主机应将名称指定为`127.0.0.1`，除非有其他配置中自定义探测。
* 确保对 http://\<host\>:\<port\>\<path\> 的调用返回 HTTP 结果代码 200。
* 确保 Interval、 超时和 UnhealtyThreshold 都在可接受的范围内。
* 如果使用 HTTPS 探测器，请通过在后端服务器本身上配置回退证书，确保后端服务器不需要 SNI。

## <a name="request-time-out"></a>请求超时

### <a name="cause"></a>原因

收到用户请求后，应用程序网关配置的规则适用于的请求，并将其路由到后端池实例。 应用程序网关将等待一段可配置的时间间隔，以接收后端实例做出的响应。 默认情况下，此时间间隔是**20**秒。 如果应用程序网关未从后端应用程序在此时间间隔内收到响应，用户请求获取 502 错误。

### <a name="solution"></a>解决方案

应用程序网关，可配置此设置通过 BackendHttpSetting，它可以然后应用到不同的池。 在不同的后端池可以具有不同的 BackendHttpSetting 和配置不同的请求超时。

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool 为空

### <a name="cause"></a>原因

如果应用程序网关没有虚拟机或虚拟机规模集配置的后端地址池中，它无法路由任何客户请求并发送错误的网关错误。

### <a name="solution"></a>解决方案

请确保后端地址池不为空。 这可以通过 PowerShell、CLI 或门户来实现。

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

上述 cmdlet 的输出应包含非空后端地址池。 下面的示例显示两个池返回该配置有 FQDN 或后端 Vm 的 IP 地址。 BackendAddressPool 的预配状态必须是 'Succeeded'。

BackendAddressPoolsText：

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>BackendAddressPool 中存在运行不正常的实例

### <a name="cause"></a>原因

如果 BackendAddressPool 的所有实例都都不正常，应用程序网关不支持将用户请求路由到任何后端。 当后端实例处于正常状态的但没有所需的应用程序部署时，这也可以是这种情况。

### <a name="solution"></a>解决方案

确定实例正常运行且已正确配置了应用程序。 检查是否后端实例可以响应 ping 同一 VNet 中的另一个 VM 中。 如果使用了公共终结点配置，请确保将浏览器请求的 web 应用程序是可维护性。

## <a name="next-steps"></a>后续步骤

如果上述步骤未解决此问题，打开[支持票证](https://azure.microsoft.com/support/options/)。

