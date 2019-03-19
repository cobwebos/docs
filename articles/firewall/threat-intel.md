---
title: Azure 防火墙威胁智能基于筛选
description: 了解有关 Azure 防火墙威胁智能筛选
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730529"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Azure 防火墙威胁智能基于筛选-公共预览版

可以为你的防火墙以发出警报并拒绝流量从/向已知恶意 IP 地址和域启用威胁智能基于筛选。 源自 Microsoft 威胁智能源的 IP 地址和域。 [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence)为 Microsoft 威胁智能提供支持，并由多个服务，包括 Azure 安全中心。

![防火墙威胁智能](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> 威胁智能基于筛选当前处于公共预览状态，并提供预览版服务级别协议。 某些功能可能不受支持或者受限。  有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果启用了威胁智能基于筛选，则关联的规则之前处理操作的 NAT 规则、 网络规则或应用程序规则。 预览期间，只有最高置信度记录包含。

您可以选择时触发一个规则，也可以选择警报并拒绝模式只记录警报。

默认情况下，在警报模式中启用威胁智能基于筛选。 无法关闭此功能或更改的模式，直到你的区域中，门户界面变得可用。

![威胁智能基于筛选门户界面](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>日志

以下日志摘录显示了触发的规则：

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

- **出站测试**-出站流量警报应该很少见，因为它意味着您的环境已遭到破坏。 若要帮助测试出站警报正在，将触发警报的测试创建 FQDN。 使用**testmaliciousdomain.eastus.cloudapp.azure.com**为出站测试。

- **入站测试**-您有望看到警报上的传入流量，如果在防火墙上配置 DNAT 规则。 即使 DNAT 规则允许仅特定源，否则拒绝流量，也是如此。 Azure 防火墙不会对所有已知的端口扫描程序; 不发出警报仅在扫描仪上的已知还参与恶意活动。

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 防火墙日志分析示例](log-analytics-samples.md)
- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)
- 查看[Microsoft 安全性智能报告](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)