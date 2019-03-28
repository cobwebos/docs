---
title: Azure 应用程序网关的 web 应用程序防火墙简介
description: 本文概述应用程序网关的 Web 应用程序防火墙 (WAF)
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 830513a03bd65ca14cb0938ae599a676f1bb3bca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518178"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Azure 应用程序网关的 web 应用程序防火墙

Azure 应用程序网关提供了进行集中的保护 web 应用程序受到的常见攻击和漏洞的 web 应用程序防火墙 (WAF)。 通过利用常见的已知漏洞的恶意攻击越来越多地针对 web 应用程序。 SQL 注入和跨站点脚本是最常见的攻击。

阻止应用程序代码中的此类攻击是一个挑战。 它可以要求严格的维护、 修补和监视应用程序拓扑的多个层。 集中式的 web 应用程序防火墙可帮助进行安全管理要简单得多。 WAF 还使应用程序管理员可以保护以防御威胁与入侵，更好的保障。

WAF 解决方案可通过集中修补已知的漏洞，而不是保护每个单独的 web 应用程序响应速度更快的安全威胁。 现有应用程序网关能够轻松转换到火灾墙上启用应用程序网关。

根据应用程序网关 WAF[核心规则集 (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 或 2.2.9 中打开 Web Application Security Project (OWASP)。 WAF 会自动更新以包括在无需额外配置的保护，防御新漏洞。

![应用程序网关 WAF 关系图](./media/waf-overview/WAF1.png)

应用程序网关用作应用程序传送控制器 (ADC)。 它提供了安全套接字层 (SSL) 终止、 基于 cookie 的会话相关性、 轮循负载分发、 基于内容的路由、 托管多个网站和安全增强功能的能力。

应用程序网关的安全增强功能包括 SSL 策略管理和端到端 SSL 支持。 应用程序的安全性得到加强的应用程序网关与 WAF 集成。 组合可保护 web 应用程序受到常见漏洞的影响。 它提供了轻松配置中心位置来管理。

## <a name="benefits"></a>权益

本部分介绍应用程序网关和其 WAF 提供的核心优势。

### <a name="protection"></a>保护

* 保护 web 应用程序免受 web 漏洞和攻击而无需对后端代码进行修改。

* 在同时保护多个 web 应用程序。 应用程序网关的实例可以托管的 web 应用程序防火墙保护的最多 20 个网站。

### <a name="monitoring"></a>监视

* 使用实时 WAF 日志监视 web 应用程序针对的攻击。 日志集成与[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)来跟踪 WAF 警报和轻松监视趋势。

* 应用程序网关 WAF 与 Azure 安全中心集成。 安全中心提供的所有 Azure 资源的安全状态的核心概况。

### <a name="customization"></a>自定义

* 您可以自定义 WAF 规则和规则组，以适应应用程序的需求，并消除误报。

## <a name="features"></a>功能

- SQL 注入保护。
- 跨站点脚本保护。
- 针对其他常见 web 攻击，例如命令注入、 HTTP 请求走私、 HTTP 响应拆分和远程文件包含的保护。
- 防止 HTTP 协议冲突。
- 防止 HTTP 协议异常行为，例如缺少主机用户代理和 accept 标头。
- 针对智能机器人、 爬网程序和扫描仪的保护。
- 检测常见应用程序错误配置 （例如，Apache 和 IIS）。
- 可配置的请求大小限制的下限和上限。
- 排除列表中，可以忽略来自 WAF 评估某些请求属性。 常见示例是用于身份验证或密码字段的 Active Directory 插入令牌。

### <a name="core-rule-sets"></a>核心规则集

应用程序网关支持 CRS 3.0 和 CRS 2.2.9 两个规则集。 这些规则保护 web 应用程序免受恶意活动。

应用程序网关 WAF 预先配置 CRS 3.0 默认情况下。 但您可以选择以改为使用 CRS 2.2.9。 CRS 3.0 减少与 CRS 2.2.9 相比的假正值。 此外可以[自定义规则，以满足您的需要](application-gateway-customize-waf-rules-portal.md)。

WAF 可保护针对以下 web 漏洞：

- SQL 注入式攻击
- 跨站点脚本攻击
- 其他常见的攻击，例如命令注入、 HTTP 请求走私、 HTTP 响应拆分和远程文件包含
- HTTP 协议冲突
- HTTP 协议异常行为，例如缺少主机用户代理和 accept 标头
- 智能机器人、 爬网程序和扫描仪
- 常见的应用程序配置错误 （例如，Apache 和 IIS）

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 包含 13 个规则组下, 表中所示。 每个组包含多个规则，可禁用。

|规则组|描述|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|锁定方法 (PUT、 PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|防范端口和环境扫描程序|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|防范协议和编码问题|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|防范标头注入、 请求走私和响应拆分|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|针对文件和路径攻击提供保护|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|远程文件包含 (RFI) 攻击|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|重新保护远程代码执行攻击|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|防范 PHP 注入攻击|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|防止跨站点脚本攻击|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|防范 SQL 注入攻击|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|防范会话固定攻击|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 包括 10 个规则组下, 表中所示。 每个组包含多个规则，可禁用。

|规则组|描述|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|防范协议冲突 （例如无效的字符或使用请求正文获取）|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|防范错误标头信息|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|防范参数或文件超出限制|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|防范受限的方法、 标头和文件类型|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|防范 web 爬网程序和扫描仪的步骤|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|防范普通攻击 （如会话固定、 远程文件包含和 PHP 注入）|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|防范 SQL 注入攻击|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|防止跨站点脚本攻击|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|防范路径遍历攻击|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|防范后门特洛伊木马|

### <a name="waf-modes"></a>WAF 模式

可以将应用程序网关 WAF 配置为在以下两种模式中运行：

* **检测模式**:监视并记录所有威胁警报。 你的应用程序网关在启用日志记录诊断**诊断**部分。 您还必须确保已选择并打开 WAF 日志。 在检测模式下运行时，web 应用程序防火墙不会阻止传入的请求。
* **阻止模式下**:块入侵和攻击检测规则。 攻击者会收到"403 未授权的访问"异常并终止连接。 阻止模式下在 WAF 日志中记录此类攻击。

### <a name="anomaly-scoring-mode"></a>异常评分模式
 
OWASP 有两种模式以决定是否要阻止的流量：传统模式和异常评分模式。

在传统模式下，与任何规则匹配的流量被视为独立于其他规则的匹配项。 此模式非常易于理解。 但缺少的有关的规则数量匹配特定请求的信息是一个限制。 因此，引入了异常评分模式。 默认值为 OWASP 3 它。*x*。

在异常评分模式下，与任何规则匹配的流量不会立即阻止防火墙在阻止模式下时。 规则具有特定严重级别：*关键*，*错误*，*警告*，或*通知*。 该严重性级别会影响用于请求，这名为异常分数的数值。 例如，一个*警告*匹配分配分数为 3 的规则。 一个*严重*匹配参与 5 的规则。

没有阻止通信异常分数为 5 的阈值。 因此，单个*严重*规则匹配足以让应用程序网关 WAF，若要阻止的请求，甚至在阻止模式下。 但一个*警告*匹配项，仅增加异常分数 3，这还不够本身来阻止流量的规则。

> [!NOTE]
> WAF 规则匹配流量时，将记录该消息包含操作值"已阻止"。 但流量是实际仅被阻止的异常情况分数为 5 或更高版本。  

### <a name="waf-monitoring"></a>WAF 监视

监视应用程序网关的运行状况非常重要。 通过与 Azure 安全中心，Azure Monitor 的集成支持监视 WAF 和它保护的应用程序的运行状况和 Azure Monitor 日志。

![应用程序网关 WAF 诊断的关系图](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

应用程序网关日志集成在一起[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)。 此功能可跟踪包括 WAF 警报和日志的诊断信息。 你可以访问此功能上**诊断**的应用程序网关资源在门户中或直接通过 Azure Monitor 中的选项卡。 若要了解有关启用日志的详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

#### <a name="azure-security-center"></a>Azure 安全中心

[安全中心](../security-center/security-center-intro.md)可帮助你防止、 检测和响应威胁。 它提供增加的可见性并控制 Azure 资源的安全性。 应用程序网关[与安全中心集成](application-gateway-integration-security-center.md)。 安全中心会扫描环境以检测未受保护的 web 应用程序。 它可以建议应用程序网关 WAF 保护这些易受攻击的资源。 直接从安全中心创建防火墙。 这些 WAF 实例与安全中心集成。 它们向发送警报和运行状况信息安全中心进行报告。

![安全中心概述窗口](./media/waf-overview/figure1.png)

#### <a name="logging"></a>日志记录

应用程序网关 WAF 提供详细报告检测到每个威胁。 与 Azure 诊断日志集成日志记录。 警报记录.json 格式。 这些日志可与 [Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)集成。

![应用程序网关诊断日志 windows](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>应用程序网关 WAF SKU 定价

是在新应用程序网关 WAF SKU。 此 SKU 的只能在 Azure 资源管理器预配模型，而不是经典部署模型中可用。 此外，WAF SKU 仅提供中型和大型应用程序网关实例大小。 应用程序网关的所有限制也都适用于 WAF SKU。

定价基于每小时的网关实例费和数据处理费用。 [应用程序网关定价](https://azure.microsoft.com/pricing/details/application-gateway/)WAF SKU 不同于标准 SKU 费用。 数据处理费用是相同的。 将没有每个规则组收费。 你可以保护同一个 web 应用程序防火墙后面的多个 web 应用程序。 不需要支付支持多个应用程序。

## <a name="next-steps"></a>后续步骤

请参阅[如何在应用程序网关上配置 web 应用程序防火墙](tutorial-restrict-web-traffic-powershell.md)。
