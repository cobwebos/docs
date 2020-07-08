---
title: Azure 防火墙 SNAT 专用 IP 地址范围
description: 你可以为 SNAT 配置 IP 地址范围。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: be2bf0f9590a23f9def44a1800338c80f69a782c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610517"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 防火墙 SNAT 专用 IP 地址范围

对于发往公共 IP 地址的所有出站流量，Azure 防火墙都提供自动 SNAT。 默认情况下，当目标 IP 地址处于每个[IANA RFC 1918](https://tools.ietf.org/html/rfc1918)的专用 ip 地址范围时，Azure 防火墙不会使用网络规则。 无论目标 IP 地址如何，始终使用[透明代理](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)来应用应用程序规则。

当你将流量直接路由到 Internet 时，此逻辑非常有效。 但是，如果已启用[强制隧道](forced-tunneling.md)，则会将 Internet 绑定的流量 Snat 转换成到 AzureFirewallSubnet 中的某个防火墙专用 IP 地址，并将源从本地防火墙中隐藏。

如果组织对专用网络使用公共 IP 地址范围，Azure 防火墙会通过 SNAT 将流量发送到 AzureFirewallSubnet 中的某个防火墙专用 IP 地址。 但是，可以将 Azure 防火墙配置为不 SNAT 公共 IP 地址范围****。

若要将 Azure 防火墙配置为永远不考虑目标 IP 地址，请使用**0.0.0.0/0**作为专用 ip 地址范围。 通过此配置，Azure 防火墙永远不能将流量直接路由到 Internet。 若要将防火墙配置为始终不考虑目标地址，请使用**255.255.255.255/32**作为专用 IP 地址范围。

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>配置 SNAT 专用 IP 地址范围-Azure PowerShell

可以使用 Azure PowerShell 为防火墙指定专用 IP 地址范围。

### <a name="new-firewall"></a>新建防火墙

对于新建防火墙，Azure PowerShell 命令如下：

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> 将其他范围添加到 Azure 防火墙中时，IANAPrivateRanges 将扩展到 Azure 防火墙上的当前默认值。 若要在专用范围规范中保留 IANAPrivateRanges 默认值，必须保留在规范中， `PrivateRange` 如以下示例中所示。

有关详细信息，请参阅 [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)。

### <a name="existing-firewall"></a>现有防火墙

若要配置现有防火墙，请使用以下 Azure PowerShell 命令：

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>模板

可以将以下内容添加到 `additionalProperties` 部分：

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>配置 SNAT 专用 IP 地址范围-Azure 门户

可以使用 Azure 门户为防火墙指定专用 IP 地址范围。

1. 选择资源组，然后选择你的防火墙。
2. 在 "**概述**" 页上的 "**专用 IP 范围**" 中，选择默认值 " **IANA RFC 1918**"。

   此时将打开 "**编辑专用 IP 前缀**" 页：

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="编辑专用 IP 前缀":::

1. 默认情况下，将配置**IANAPrivateRanges** 。
2. 编辑环境的专用 IP 地址范围，然后选择 "**保存**"。

## <a name="next-steps"></a>后续步骤

- 了解[Azure 防火墙强制隧道](forced-tunneling.md)。