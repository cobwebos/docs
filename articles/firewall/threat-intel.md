---
title: 基于 Azure 防火墙威胁情报的筛选
description: 可以为防火墙启用基于威胁智能的筛选，以提醒和拒绝来自/到达已知恶意 IP 地址和域的流量。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168671"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>基于 Azure 防火墙威胁智能的筛选

可以为防火墙启用基于威胁智能的筛选，以提醒和拒绝来自/到达已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。 [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) 为 Microsoft 威胁智能助力，它已得到 Azure 安全中心等多项服务的运用。

![防火墙威胁情报](media/threat-intel/firewall-threat.png)

如果已启用基于威胁智能的筛选，则将在任何 NAT 规则、网络规则或应用程序规则之前处理相关规则。

可以选择仅在触发规则时记录警报，或者选择警报和拒绝模式。

默认情况下，基于威胁情报的筛选将在警报模式下启用。 你无法关闭此功能或更改模式，直到门户界面在你的区域中可用。

![基于威胁情报的筛选门户界面](media/threat-intel/threat-intel-ui.png)

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

- **出站测试** - 出站流量警报极少发生，因为这意味着环境已遭到入侵。 为了帮助测试出站警报是否正常运行，已创建一个用于触发警报的测试 FQDN。 使用**testmaliciousdomain.eastus.cloudapp.azure.com**进行出站测试。

- **入站测试** - 如果在防火墙上配置了 DNAT 规则，则预期可以看到针对传入流量的警报。 即使 DNAT 规则中仅允许特定的源，并拒绝其他源的流量，也是如此。 Azure 防火墙不会在所有已知的端口扫描程序上发出警报;仅在已知的扫描程序中，还会参与恶意活动。

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 防火墙 Log Analytics 示例](log-analytics-samples.md)
- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)
- 查看 [Microsoft 安全情报](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)