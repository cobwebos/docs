---
title: Azure 防火墙 SNAT 专用 IP 地址范围
description: 可以配置 IP 地址专用范围，使防火墙不会将流量 SNAT 到这些 IP 地址。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444454"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 防火墙 SNAT 专用 IP 地址范围

当目标 IP 地址处于每个[IANA RFC 1918](https://tools.ietf.org/html/rfc1918)的专用 ip 地址范围时，Azure 防火墙不会使用 SNAT。 

如果组织对专用网络使用公共 IP 地址范围，Azure 防火墙会通过 SNAT 将流量发送到 AzureFirewallSubnet 中的某个防火墙专用 IP 地址。 但是，可以将 Azure 防火墙配置为**不**将公共 IP 地址范围设置为 SNAT。

## <a name="configure-snat-private-ip-address-ranges"></a>配置 SNAT 专用 IP 地址范围

你可以使用 Azure PowerShell 来指定防火墙不会使用的 IP 地址范围。

### <a name="new-firewall"></a>新防火墙

对于新的防火墙，Azure PowerShell 命令为：

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> 将其他范围添加到 Azure 防火墙中时，IANAPrivateRanges 将扩展到当前默认值。

有关详细信息，请参阅[AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)。

### <a name="existing-firewall"></a>现有防火墙

若要配置现有防火墙，请使用以下 Azure PowerShell 命令：

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>模板

可以将以下内容添加到 `additionalProperties` 部分：

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。