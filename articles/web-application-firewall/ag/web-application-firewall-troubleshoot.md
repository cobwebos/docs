---
title: 故障排除 - Azure Web 应用程序防火墙
description: 本文提供 Azure 应用程序网关的 Web 应用程序防火墙 (WAF) 的故障排除信息
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 6fa959b1c9ed021a97031ba03822ae89fbbb7bbb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983068"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>排查 Azure 应用程序网关的 Web 应用程序防火墙 (WAF) 问题

如果本应通过 Web 应用程序防火墙 (WAF) 的请求被阻止，可以采取以下几种方法来解决。

首先，请确保已阅读 [WAF 概述](ag-overview.md)和 [WAF 配置](application-gateway-waf-configuration.md)文档。 此外，确保已启用 [WAF 监视](../../application-gateway/application-gateway-diagnostics.md)。这些文章介绍了 WAF 函数、WAF 规则集的工作原理，以及 WAF 日志的访问方式。

OWASP 规则集的设计目的是非常严格，可以根据使用 WAF 的应用程序或组织的特定需求进行调整。 在许多情况下，它是完全正常的，但实际上却是预期的：创建排除项、自定义规则，甚至禁用可能导致问题或误报的规则。 每个站点和每个 URI 的策略允许这些更改仅影响特定的站点/Uri，因此，任何更改都不会影响可能不会遇到相同问题的其他站点。 

## <a name="understanding-waf-logs"></a>了解 WAF 日志

WAF 日志用于显示 WAF 匹配或阻止的每个请求。 它是匹配或阻止的所有已评估请求的账本。 如果你发现 WAF 阻止了原本不应该阻止的请求（误报），可以采取几种方法来解决问题。 首先缩小查找范围，找到特定的请求。 浏览日志，找到该请求的特定 URI、时间戳或事务 ID。 找到关联的日志条目后，可以开始处理误报。

例如，假设某个合法流量（你希望该流量通过 WAF）包含字符串 *1=1*。 如果尝试请求，WAF 将阻止任何参数或字段中包含 *1=1* 字符串的流量。 此字符串通常与 SQL 注入攻击相关。 可以浏览日志，查看请求的时间戳，以及阻止/匹配的规则。

在以下示例中可以看到，处理同一请求期间触发了 4 个规则（使用 TransactionId 字段）。 第一个规则指出该请求是匹配的，因为用户对该请求使用了数字/IP URL，这将异常评分增加了 3 分（因为这是一条警告）。 匹配的下一个规则是 942130，即你要查找的规则。 可以在 `details.data` 字段中看到 *1=1*。 这进一步将异常评分增加了 3 分，因为这也是一条警告。 通常，具有“已匹配”操作的每个规则都会增加异常评分，此时，异常评分为 6。**** 有关详细信息，请参阅[异常评分模式](ag-overview.md#anomaly-scoring-mode)。

最后两个日志条目显示该请求被阻止，因为异常评分足够高。 这些条目的操作不同于另外两个条目。 它们显示确实阻止了该请求。** 这些规则是必需的，不可禁用。 不应将它们视为规则，而应更多地将其视为 WAF 内部组件的核心基础结构。

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

## <a name="fixing-false-positives"></a>修正误报

根据此信息，以及规则 942130 是匹配 *1=1* 字符串的这种认知，可以采取几项措施来避免此规则阻止流量：

- 使用排除列表

   有关排除列表的详细信息，请参阅 [WAF 配置](application-gateway-waf-configuration.md#waf-exclusion-lists)。
- 禁用规则。

### <a name="using-an-exclusion-list"></a>使用排除列表

若要明智地决定如何处理误报，必须熟悉应用程序所用的技术。 例如，假设技术堆栈中没有 SQL 服务器，但你收到了与这些规则相关的误报。 禁用这些规则不一定会削弱安全性。

使用排除列表的一个好处是可以仅禁用请求的特定部分。 但这意味着，特定的排除项将适用于通过 WAF 的所有流量，因为这是一项全局设置。 例如，如果对于特定的应用而言，*1=1* 是正文中的有效请求，但对于其他应用而言不是有效的请求，则可能会导致问题。 另一个好处是，如果满足特定的条件，则可以在正文、标头和 Cookie 之间选择要排除的部分，而不用排除整个请求。

特定的参数偶尔会以一种不直观的方式传入 WAF。 例如，使用 Azure Active Directory 进行身份验证时会传递一个令牌。 此令牌 *__RequestVerificationToken* 通常以请求 Cookie 的形式传入。 但是，在某些情况下禁用 Cookie 时，此令牌还会以请求属性或“参数”的形式传递。 如果存在这种情况，需确保同时将 *__RequestVerificationToken* 作为**请求属性名称**添加到排除列表中。

![排除项](../media/web-application-firewall-troubleshoot/exclusion-list.png)

在此示例中，你希望排除等于 *text1* 的**请求属性名称**。 这一点很明显，因为你可以在防火墙日志中看到属性名称：**数据：匹配的数据： 1 = 1，位于参数： text1： 1 = 1 中**。 属性为 **text1**。 还可以通过其他几种方法找到此属性名称，具体请参阅[查找请求属性名称](#finding-request-attribute-names)。

![WAF 排除列表](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>禁用规则

避免误报的另一种方式是禁用被 WAF 认为是恶意的输入中匹配的规则。 由于你已分析 WAF 日志，并将规则查找范围缩小为 942130，因此可以在 Azure 门户中禁用它。 请参阅[通过 Azure 门户自定义 Web 应用程序防火墙规则](application-gateway-customize-waf-rules-portal.md)。

禁用规则的一个好处是，如果你知道包含特定条件并且往往会被阻止的所有流量是有效的流量，则可以针对整个 WAF 禁用该规则。 但是，如果它只是特定用例中的有效流量，则针对整个 WAF 禁用该规则会开放一个漏洞，因为这是一项全局设置。

若要使用 Azure PowerShell，请参阅[通过 PowerShell 自定义 Web 应用程序防火墙规则](application-gateway-customize-waf-rules-powershell.md)。 若要使用 Azure CLI，请参阅[通过 Azure CLI 自定义 Web 应用程序防火墙规则](application-gateway-customize-waf-rules-cli.md)。

![WAF 规则](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>查找请求属性名称

可以借助 [Fiddler](https://www.telerik.com/fiddler) 检查单个请求，并确定要调用网页的哪些特定字段。 此工具可以帮助你使用排除列表从检查项中排除某些字段。

在此示例中可以看到，输入了 *1=1* 字符串的字段名为 **text1**。

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

这是一个可以排除的字段。 若要详细了解排除列表，请参阅 [Web 应用程序防火墙请求大小限制和排除列表](application-gateway-waf-configuration.md#waf-exclusion-lists)。 在本例中，可以通过配置以下排除项来排除评估：

![WAF 排除](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

还可以检查防火墙日志来获取信息，以确定需要将哪些内容添加到排除列表。 若要启用日志记录，请参阅[应用程序网关的后端运行状况、资源日志和指标](../../application-gateway/application-gateway-diagnostics.md)。

检查防火墙日志，并在 PT1H.json 文件中查看要检查的请求的发生时间（小时）。

在此示例中可以看到，有 4 个具有相同 TransactionID 的规则，它们的发生时间完全相同：

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

了解 CRS 规则设置的工作原理，以及 CRS 规则集3.0 如何与异常计分系统一起使用（请参阅[Azure 应用程序网关的 Web 应用程序防火墙](ag-overview.md)）你知道具有 "**操作：阻塞**" 属性的下面两个规则将基于总异常分数阻止。 要关注的规则是最前面的两个规则。

记录第一个条目的原因是用户使用数字 IP 地址导航到了应用程序网关，在本例中可以忽略此条目。

需要关注的是第二个条目（规则 942130）。 在详细信息中可以看到，它匹配了某个模式 (1=1)，而字段名为 **text1**。 遵循前面的相同步骤排除**等于** **1=1** 的**请求属性名称**。

## <a name="finding-request-header-names"></a>查找请求标头名称

在 Fiddler 中还能够很方便地查找请求标头名称。 在以下屏幕截图中，可以看到此 GET 请求的标头，其中包括 *Content-Type*、*User-Agent* 等。

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

查看请求和响应标头的另一种方式是使用 Chrome 的开发人员工具。 可以按 F12 或右键单击 >**检查** -> **开发人员工具**，然后选择 "**网络**" 选项卡。加载网页，然后单击要检查的请求。

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>查找请求 Cookie 名称

如果请求包含 Cookie，可以选择“Cookie”选项卡以在 Fiddler 中查看 Cookie。****

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>限制全局参数以消除误报

- 禁用请求正文检查

   将“检查请求正文”设置为“关闭”时，WAF 不会评估所有流量的请求正文。**** 如果你知道请求正文对你的应用程序而言不是恶意的，这此设置可能很有用。

   如果禁用此选项，则只是不检查请求正文。 除非使用排除列表功能排除了单个标头和 Cookie，否则仍会检查标头和 Cookie。

- 文件大小限制

   限制 WAF 的文件大小可以限制 Web 服务器遭受攻击的可能性。 如果允许上传大文件，后端瘫痪的风险就会增大。 根据应用程序的用例将文件大小限制为一般大小是防止攻击的另一种方式。

   > [!NOTE]
   > 如果你知道你的应用永远不需要上传超过给定大小的任何文件，可以通过设置一个限制来控制风险。

## <a name="firewall-metrics-waf_v1-only"></a>防火墙指标（仅 WAF_v1）

对于 v1 Web 应用防火墙，门户中现在提供以下指标： 

1. Web 应用程序防火墙阻止的请求计数   被阻止的请求数
2. Web 应用程序防火墙阻止的规则计数   阻止请求**和**匹配的所有规则
3. Web 应用程序防火墙规则分配总数   在评估过程中匹配的所有规则
     
若要启用指标，请在门户中选择“指标”**** 选项卡，然后选择三个指标之一。

## <a name="next-steps"></a>后续步骤

参阅[如何在应用程序网关上配置 Web 应用程序防火墙](tutorial-restrict-web-traffic-powershell.md)。
