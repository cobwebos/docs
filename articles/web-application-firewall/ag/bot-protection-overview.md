---
title: Azure 应用程序网关上的 WAF 机器人防护概述
titleSuffix: Azure Web Application Firewall
description: 本文概述了应用程序网关上的 Web 应用程序防火墙 (WAF) 机器人防护
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 05/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e01f9ac8966223e11ad218af7bf6fbb2462f28f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83714893"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure 应用程序网关上的 Azure Web 应用程序防火墙机器人防护概述

大约有 20% 的 Internet 流量来自不良机器人。 它们执行一些操作，例如抓取、扫描和查找 Web 应用程序中的漏洞。 当这些机器人受到 Web 应用程序防火墙 (WAF) 的阻止时，则无法攻击你。 它们还将无法使用你的资源和服务，例如后端和其他底层基础结构。

可以为 WAF 启用托管机器人防护规则集，以便阻止或记录来自已知恶意 IP 地址的请求。 IP 地址源自 Microsoft 威胁智能源。 Intelligent Security Graph 为 Microsoft 威胁智能助力，它已得到 Azure Security Center 等多项服务的运用。

> [!IMPORTANT]
> 机器人防护规则集当前为公共预览版，并提供预览版服务级别协议。 某些功能可能不受支持或者受限。 有关详细信息，请参阅  [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 。

## <a name="use-with-owasp-rulesets"></a>配合使用 OWASP 规则集

可以将机器人防护规则集与任何 OWASP 规则集（2.2.9、3.0 和 3.1）配合使用。 在任意给定时间，只能使用一个 OWASP 规则集。 机器人防护规则集包含另一个规则，该规则显示在其自己的规则集中。 该规则名为 Microsoft_BotManagerRuleSet_0.1，其启用或禁用方式与其他 OWASP 规则一样。

![机器人规则集](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>规则集更新

已知不良 IP 地址的机器人缓解规则集列表每天从 Microsoft 威胁智能源更新多次，以便与机器人保持同步。 即使机器人攻击途径发生变化，你的 Web 应用程序也会持续受到保护。

## <a name="log-example"></a>日志示例

下面是机器人防护的日志条目示例：

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>后续步骤

- [为 Azure 应用程序网关上的 Web 应用程序防火墙配置机器人防护（预览）](bot-protection.md)
