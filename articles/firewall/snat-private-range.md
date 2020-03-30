---
title: Azure 防火墙 SNAT 专用 IP 地址范围
description: 您可以配置 IP 地址专用范围，以便防火墙不会将 SNAT 流量连接到这些 IP 地址。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064810"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 防火墙 SNAT 专用 IP 地址范围

当目标 IP 地址位于[IANA RFC 1918](https://tools.ietf.org/html/rfc1918)的专用 IP 地址范围内时，Azure 防火墙不会使用网络规则进行 SNAT。 应用程序规则始终使用[透明代理](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)应用，而不考虑目标 IP 地址。

如果组织对专用网络使用公共 IP 地址范围，Azure 防火墙会通过 SNAT 将流量发送到 AzureFirewallSubnet 中的某个防火墙专用 IP 地址。 但是，您可以将 Azure 防火墙配置为**不**将公共 IP 地址范围 SNAT。

## <a name="configure-snat-private-ip-address-ranges"></a>配置 SNAT 专用 IP 地址范围

可以使用 Azure PowerShell 指定防火墙不会 SNAT 的 IP 地址范围。

### <a name="new-firewall"></a>新防火墙

对于新防火墙，Azure PowerShell 命令为：

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges 扩展为 Azure 防火墙上的当前默认值，同时将其他范围添加到其中。

有关详细信息，请参阅新[A 防火墙](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)。

### <a name="existing-firewall"></a>现有防火墙

要配置现有防火墙，请使用以下 Azure PowerShell 命令：

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>模板

您可以将以下内容添加到该`additionalProperties`部分：

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。