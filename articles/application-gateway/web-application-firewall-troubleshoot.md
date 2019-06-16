---
title: 排查 Azure 应用程序网关的 Web 应用程序防火墙
description: 本文提供 Azure 应用程序网关的故障排除信息的 Web 应用程序防火墙 (WAF)
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082438"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>排查 Azure 应用程序网关的 Web 应用程序防火墙 (WAF)

有如果会阻止请求应通过通过 Web 应用程序防火墙 (WAF) 可以执行的几项操作。

首先，请确保你已阅读[WAF 概述](waf-overview.md)并[WAF 配置](application-gateway-waf-configuration.md)文档。 此外，请确保已启用[WAF 监视](application-gateway-diagnostics.md)这些文章介绍了如何 WAF 函数时，WAF 规则如何设置工作，以及如何访问 WAF 日志。

## <a name="understanding-waf-logs"></a>理解 WAF 日志

WAF 日志的用途是显示每个请求都是匹配或阻止的 WAF。 它是所有计算的请求匹配或阻止的分类帐。 如果您注意到此 WAF 阻止它不应 （误报） 的请求，您可以做一些事情。 首先，缩小，并查找特定的请求。 仔细查看日志以查找请求的特定 URI、 时间戳或事务 ID。 找到关联的日志条目后，你可以开始作用于的假正值。

例如，假设你有合法的流量包含字符串*1 = 1*想要通过 WAF。 如果重试请求，WAF 会阻止流量包含你*1 = 1*中任何参数或字段的字符串。 这是与 SQL 注入攻击通常关联的字符串。 您可以仔细查看日志并查看请求和阻止/匹配的规则的时间戳。

在以下示例中，可以看到在同一请求 （使用 TransactionId 字段） 期间触发了四个规则。 第一个说它与匹配，因为该用户所使用数字/IP 请求，这会异常分数增加 3，因为它是一条警告的 URL。 匹配的下一个规则是 942130，即你正在寻找的一个。 您可以看到*1 = 1*中`details.data`字段。 这进一步增加了异常情况分数由三个同样，因为它也是一条警告。 通常情况下，每个操作的规则**Matched**增加的异常分数，并且此时异常分数将六个。 有关详细信息，请参阅[异常评分模式](waf-overview.md#anomaly-scoring-mode)。

最后两个日志条目显示异常分数得足够高，因为请求已被阻止。 这些条目具有比其他两个不同的操作。 它们实际上显示他们*阻止*请求。 这些规则都是必需的并且不能禁用。 它们不应被认为是作为规则，而是更作为 WAF 内部机制的核心基础结构。

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>修复的假正值

此信息和知识，该规则 942130 是匹配的那个*1 = 1*字符串，您可以做一些事情，若要阻止这阻止你的流量：

- 使用排除列表

   请参阅[WAF 配置](application-gateway-waf-configuration.md#waf-exclusion-lists)有关排除的详细信息列出了。
- 禁用的规则。

### <a name="using-an-exclusion-list"></a>使用排除列表

若要做出明智的决策有关处理一个 false 正数，务必熟悉你的应用程序使用的技术。 例如，假设您的技术堆栈，没有 SQL server 并收到与这些规则相关的假正值。 禁用这些规则并不一定是降低你的安全。

使用排除列表的一个好处是请求的特定部分请求的被禁用。 但是，这意味着特定排除适用于所有流量通过 WAF，因为它是一种全局设置。 例如，这可能会导致问题如果*1 = 1*为对于某些应用，但没有为其他主体中有效的请求。 另一个好处是，您可以选择正文、 标头和 cookie 之间是否满足特定条件，而不是排除整个请求中排除。

有时，有些情况下，获取特定的参数传递到可能不是直观的方式 WAF。 例如，没有使用 Azure Active Directory 进行身份验证时，获取传递的令牌。 此令牌*为 __RequestVerificationToken*，通常获取作为传入请求的 Cookie。 但是，在某些情况下，禁用了 cookie，此令牌还为请求属性或"arg"传递。 如果发生这种情况，您需要确保*为 __RequestVerificationToken*添加到排除列表作为**请求属性名称**也。

![排除在外](media/waf-tshoot/exclusion-list.png)

在此示例中，你想要排除**请求属性名称**的等于*text1*。 这是显而易见的因为你可以查看防火墙日志中的属性名称：**数据：匹配的数据：1 = 1 ARGS:text1 中找到：1=1**. 该属性是**text1**。 此外可以找到此属性名称几个方法，请参阅[查找属性名称的请求](#finding-request-attribute-names)。

![WAF 排除列表](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>禁用规则

另一种方法来避开假正为以禁用输入此 WAF 思想匹配规则是恶意。 由于 WAF 日志进行分析，并且已经将目标至 942130 规则，则可以在 Azure 门户中将其禁用。 请参阅[自定义通过 Azure 门户的 web 应用程序防火墙规则](application-gateway-customize-waf-rules-portal.md)。

禁用的规则的一个好处是，如果您知道包含通常被阻止的某一特定条件的所有流量都是有效的通信，则可以禁用该规则，整个 waf。 但是，如果它只是有效的通信，在特定用例中，打开一个漏洞通过整个 waf 禁用该规则，因为它是一种全局设置。

如果想要使用 Azure PowerShell，请参阅[自定义 web 应用程序防火墙规则通过 PowerShell](application-gateway-customize-waf-rules-powershell.md)。 如果你想要使用 Azure CLI，请参阅[自定义 web 应用程序防火墙规则通过 Azure CLI](application-gateway-customize-waf-rules-cli.md)。

![WAF 规则](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>查找请求属性名称

借助[Fiddler](https://www.telerik.com/fiddler)，检查各个请求并决定称为 web 页面的哪些特定字段。 这可以帮助从检查使用排除列表中排除某些字段。

在此示例中，可以看到该字段位置*1 = 1*输入的字符串称为**text1**。

![Fiddler](media/waf-tshoot/fiddler-1.png)

这是可以排除的字段。 若要了解有关排除列表的详细信息，请参阅[Web 应用程序防火墙请求大小限制和排除列表](application-gateway-waf-configuration.md#waf-exclusion-lists)。 你可以通过配置以下排除在这种情况下排除计算：

![WAF 排除](media/waf-tshoot/waf-exclusion-02.png)

您还可以检查防火墙日志以获取信息以确定您需要将添加到排除列表。 若要启用日志记录，请参阅[后端运行状况、 诊断日志和应用程序网关指标](application-gateway-diagnostics.md)。

检查防火墙日志，并在你想要检查的请求出现，每小时查看 PT1H.json 文件。

在此示例中，可以看到，使用相同的 TransactionID，四个规则，并且它们都发生在完全相同的时间：

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

你知道如何与 CRS 规则集的工作，和 CRS 规则集 3.0 配合评分系统异常 (请参阅[Azure 应用程序网关的 Web 应用程序防火墙](waf-overview.md)) 您知道底部的两个规则使用**操作：阻止**属性正在阻止基于总异常分数。 规则可以专注于两个提供。

第一个条目被记录，因为用户使用数字 IP 地址进行导航到应用程序网关，可以在这种情况下将其忽略。

第二个之一 （规则 942130） 是有趣的一个。 匹配模式 (1 = 1) 和名为字段中，可以看到在详细信息**text1**。 请按照相同的上一步骤中排除**请求属性名称**的**等于** **1 = 1**。

## <a name="finding-request-header-names"></a>查找请求标头名称

Fiddler 是一个有用的工具，再一次来查找请求标头名称。 在以下屏幕截图中，您可以看到此 GET 请求，其中包括标头*Content-type*，*用户代理*，依次类推。

![Fiddler](media/waf-tshoot/fiddler-2.png)

若要查看请求和响应标头的另一种方法是查看 Chrome 开发人员工具。 可按 F12 或右键单击-> **Inspect** -> **开发人员工具**，然后选择**网络**选项卡。加载 web 页上，然后单击你想要检查的请求。

![Chrome F12](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>查找请求 cookie 名称

如果该请求包含 cookie **Cookie**选项卡可以选择在 Fiddler 中查看它们。

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>限制要消除误报的全局参数

- 禁用请求正文检查

   通过设置**检查请求正文**关闭状态，所有流量请求正文将不评估 WAF。 这可能是非常有用，如果您知道请求正文不是恶意到你的应用程序。

   通过禁用此选项，不检查仅在请求正文。 标头和 cookie 保持经检查，除非单个备忘录会排除使用排除列表的功能。

- 文件大小限制

   通过限制文件大小 waf，要限制对 web 服务器发生攻击的可能性。 通过允许较大的文件上传后, 端瘫痪的风险也会增加。 限制为你的应用程序正常使用大小写的文件大小是另一种方式防止攻击。

   > [!NOTE]
   > 如果您知道您的应用程序始终不需要任何文件上传超过给定数量，你可以限制，通过设置限制。

## <a name="next-steps"></a>后续步骤

参阅[如何在应用程序网关上配置 Web 应用程序防火墙](tutorial-restrict-web-traffic-powershell.md)。
