---
title: 排查网关错误错误-Azure 应用程序网关
description: 了解如何排查应用程序网关服务器错误： 502-Web 服务器在充当网关或代理服务器时收到了无效响应。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: a48ed39af243296bcb76cb61f1fe64e4e95ab7e7
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801733"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>排查应用程序网关中的网关无效错误
<p class="alert is-flex is-primary"><span class="has-padding-left-medium has-padding-top-extra-small"><a class="button is-primary" href="https://azurevirtualsupportagent.services.microsoft.com?content=66c070b6-1c47-4c7f-b928-317a8c8b452f" target='_blank'>Start</a></span><span class="has-padding-small">通过使用虚拟代理运行<b>自动诊断</b>，快速开始解决问题。</span><div align="right"> <sub>Privacy Statement</sub>隐私声明<span class="has-padding-small"> <a href="https://privacy.microsoft.com/privacystatement" target='_blank'></div></a></span></p>
了解如何排查使用 Azure 应用程序网关时收到的网关无效 (502) 错误。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概述

配置应用程序网关后，你可能会看到 "服务器错误： 502-Web 服务器在作为网关或代理服务器时收到了无效响应" 的一个错误。 此错误可能是以下主要原因造成的：

* NSG、UDR 或自定义 DNS 阻止了对后端池成员的访问。
* 虚拟机规模集的后端 VM 或实例未响应默认的运行状况探测。
* 自定义运行状况探测的配置无效或不正确。
* Azure 应用程序网关的[后端池未配置或为空](#empty-backendaddresspool)。
* [虚拟机规模集](#unhealthy-instances-in-backendaddresspool)中没有正常运行的 VM 或实例。
* 请求超时或用户请求出现[连接问题](#request-time-out)。

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>网络安全组、用户定义的路由或自定义 DNS 问题

### <a name="cause"></a>原因

如果对后端的访问由于存在 NSG、UDR 或自定义 DNS 而被阻止，则应用程序网关实例将无法联系后端池。 这会导致探测故障并引发 502 错误。

NSG/UDR 可能存在于应用程序网关子网中，也可能存在于部署了应用程序 VM 的子网中。

类似地，VNet 中存在自定义 DNS 也可能会导致问题。 用于后端池成员的 FQDN 无法由用户为 VNet 配置的 DNS 服务器正确解析。

### <a name="solution"></a>解决方案

通过执行以下步骤验证 NSG、UDR 和 DNS 配置：

* 检查与应用程序网关子网关联的 NSG。 确保与后端的通信没有被阻止。
* 检查与应用程序网关子网关联的 UDR。 确保 UDR 没有将流量引离后端子网。 例如，检查到网络虚拟设备的路由或通过 ExpressRoute/VPN 播发到应用程序网关子网的默认路由。

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
如果存在，请确保 DNS 服务器能够正确解析后端池成员的 FQDN。

## <a name="problems-with-default-health-probe"></a>默认运行状况探测出现问题

### <a name="cause"></a>原因

此外，出现 502 错误经常意味着默认的运行状况探测无法访问后端 VM。

预配某个应用程序网关实例时，该实例会使用 BackendHttpSetting 的属性自动将默认的运行状况探测配置到每个 BackendAddressPool。 无需用户输入即可设置此探测。 具体而言，在配置负载均衡规则时，会在 BackendHttpSetting 与 BackendAddressPool 之间建立关联。 默认探测是针对其中每个关联配置的，而应用程序网关会在 BackendHttpSetting 元素中指定的端口上，与 BackendAddressPool 中每个实例发起周期性运行状况检查连接。 

下表列出了与默认运行状况探测关联的值：

| 探测属性 | “值” | 说明 |
| --- | --- | --- |
| 探测 URL |`http://127.0.0.1/` |URL 路径 |
| 时间间隔 |30 |探测间隔（秒） |
| 超时 |30 |探测超时（秒） |
| 不正常阈值 |3 |探测重试计数。 连续探测失败计数达到不正常阈值后，将后端服务器标记为故障。 |

### <a name="solution"></a>解决方案

* 确定默认站点已配置且正在侦听 127.0.0.1。
* 如果 BackendHttpSetting 指定的端口不是 80，则应将默认站点配置为侦听指定的端口。
* 对 `http://127.0.0.1:port` 的调用应返回 HTTP 结果代码 200。 应在 30 秒超时期限内返回此代码。
* 确保配置的端口已打开，并且没有任何防火墙或 Azure 网络安全组在配置的端口上阻止传入或传出流量。
* 如果对 Azure 经典 VM 或云服务使用 FQDN 或公共 IP，请确保打开相应的[终结点](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json)。
* 如果 VM 是通过 Azure 资源管理器配置的并且位于应用程序网关部署所在的 VNet 的外部，则必须将[网络安全组](../virtual-network/security-overview.md)配置为允许在所需端口上进行访问。

## <a name="problems-with-custom-health-probe"></a>自定义运行状况探测出现问题

### <a name="cause"></a>原因

自定义运行状况探测能够对默认探测行为提供更大的弹性。 使用自定义探测时，可以配置探测间隔、要测试的 URL 和路径，以及在将后端池实例标记为不正常之前可接受的失败响应次数。

添加了以下附加属性：

| 探测属性 | 说明 |
| --- | --- |
| 名称 |探测的名称。 此名称用于在后端 HTTP 设置中引用探测。 |
| 协议 |用于发送探测的协议。 探测使用后端 HTTP 设置中定义的协议 |
| 主机 |用于发送探测的主机名。 仅当应用程序网关上配置了多站点时才适用。 这与 VM 主机名不同。 |
| 路径 |探测的相对路径。 有效路径以“/”开头。 将探测发送到 \<protocol\>://\<host\>:\<port\>\<path\> |
| 时间间隔 |探测间隔（秒）。 这是每两次连续探测之间的时间间隔。 |
| 超时 |探测超时（秒）。 如果在此超时期间内未收到有效响应，则将探测标记为失败。 |
| 不正常阈值 |探测重试计数。 连续探测失败计数达到不正常阈值后，将后端服务器标记为故障。 |

### <a name="solution"></a>解决方案

根据上表验证是否已正确配置自定义运行状况探测。 除了上述故障排除步骤以外，另请确保符合以下要求：

* 确保已根据[指南](application-gateway-create-probe-ps.md)正确指定了探测。
* 如果在应用程序网关中设置了单站点，则默认情况下，除非已在自定义探测中进行配置，否则应将主机名指定为 `127.0.0.1`。
* 确保对 http://\<host\>:\<port\>\<path\> 的调用返回 HTTP 结果代码 200。
* 确保 Interval、Timeout 和 UnhealtyThreshold 都在可接受的范围内。
* 如果使用 HTTPS 探测器，请通过在后端服务器本身上配置回退证书，确保后端服务器不需要 SNI。

## <a name="request-time-out"></a>请求超时

### <a name="cause"></a>原因

收到用户请求后，应用程序网关会将配置的规则应用到该请求，并将其路由到后端池实例。 应用程序网关将等待一段可配置的时间间隔，以接收后端实例做出的响应。 默认情况下，此间隔为 **20** 秒。 如果应用程序网关在此时间间隔内未收到后端应用程序的响应，则用户请求出现 502 错误。

### <a name="solution"></a>解决方案

应用程序网关允许通过 BackendHttpSetting 配置此设置，并可将此设置应用到不同的池。 不同的后端池可以有不同的 BackendHttpSetting，因此可配置不同的请求超时。

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool 为空

### <a name="cause"></a>原因

如果应用程序网关没有在后端地址池中配置 VM 或虚拟机规模集，则无法路由任何客户请求，并将发出网关无效错误。

### <a name="solution"></a>解决方案

确保后端地址池不为空。 这可以通过 PowerShell、CLI 或门户来实现。

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

上述 cmdlet 的输出应包含非空后端地址池。 以下示例显示了返回的两个池，其中配置了后端 VM 的 FQDN 或 IP 地址。 BackendAddressPool 的预配状态必须是 'Succeeded'。

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

如果 BackendAddressPool 的所有实例都运行不正常，则应用程序网关不会包含任何要将用户请求路由到其中的后端。 当后端实例运行正常但尚未部署所需的应用程序时，也可能会发生此情况。

### <a name="solution"></a>解决方案

确定实例正常运行且已正确配置了应用程序。 检查后端实例是否能够从同一个 VNet 中的另一个 VM 响应 ping。 如果实例中配置了公共终结点，请确保能够为发送到 Web 应用程序的浏览器请求提供服务。

## <a name="next-steps"></a>后续步骤

如果上述步骤无法解决问题，请开具[支持票证](https://azure.microsoft.com/support/options/)。

