---
title: Azure 防火墙威胁情报配置
description: 了解如何为 Azure 防火墙策略配置基于威胁智能的筛选，以发出警报，并拒绝从和到已知恶意 IP 地址和域的流量。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: a663c5f3bcf3492c4a9bc74fe93c6ed6a86137ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530635"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure 防火墙威胁情报配置

可以为 Azure 防火墙策略配置基于威胁情报的筛选，以针对来自和发往已知恶意 IP 地址和域的流量发出警报并将其拒绝。 IP 地址和域源自 Microsoft 威胁智能源。 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) 为 Microsoft 威胁智能助力，它已得到 Azure 安全中心等多项服务的运用。<br>

如果已配置了基于威胁情报的筛选，则会先处理关联的规则，然后再处理任何 NAT 规则、网络规则或应用程序规则。

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="威胁情报策略":::

## <a name="threat-intelligence-mode"></a>威胁情报模式

可以选择在触发规则时仅记录警报，也可以选择“发出警报并拒绝”模式。

默认情况下，基于威胁情报的筛选将在警报模式下启用。

## <a name="allowed-list-addresses"></a>允许列表地址

可以配置允许的 IP 地址列表，使威胁情报不会筛选任何指定的地址、范围或子网。



## <a name="logs"></a>日志

以下日志摘录显示了一个触发的规则：

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>测试

- 出站测试 - 出站流量警报应该比较罕见，因为这意味着环境已泄露。 为了帮助测试出站警报是否正常工作，已创建一个触发警报的测试 FQDN。 使用 **testmaliciousdomain.eastus.cloudapp.azure.com** 进行出站测试。

- 入站测试 - 如果在防火墙上配置了 DNAT 规则，则预计可以看到传入流量的警报。 即使只允许在 DNAT 规则中使用特定源也是如此，否则流量会被拒绝。 Azure 防火墙不会在所有已知的端口扫描仪上发出警报；仅在已知也会参与恶意活动的扫描仪上发出警报。

## <a name="next-steps"></a>后续步骤

- 查看 [Microsoft 安全智能报告](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)