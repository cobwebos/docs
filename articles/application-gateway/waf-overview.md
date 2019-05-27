---
title: Azure 应用程序网关的 Web 应用程序防火墙简介
description: 本文概述应用程序网关的 Web 应用程序防火墙 (WAF)
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 9c2759222198f5df682d9e7a5363c0d9679e0fad
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991407"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Azure 应用程序网关的 Web 应用程序防火墙

Azure 应用程序网关提供一个 Web 应用程序防火墙 (WAF) 用于集中保护 Web 应用程序，使其免受常见攻击和漏洞的侵害。 Web 应用程序正逐渐成为利用常见已知漏洞的恶意攻击的目标。 最常见的攻击包括 SQL 注入和跨站点脚本。

防止应用程序代码遭受此类攻击颇具挑战性。 这可能需要对应用程序拓扑的多个层进行严格的维护、修补和监视。 集中式 Web 应用程序防火墙有助于大幅简化安全管理。 WAF 还能为抵卸威胁和入侵的应用程序管理员提供更好的保障。

相较保护每个单独的 Web 应用程序，WAF 解决方案可以通过集中修补已知漏洞，更快地对安全威胁做出反应。 可将现有应用程序网关轻松转换为支持防火墙的应用程序网关。

应用程序网关 WAF 基于 开放 Web 应用程序安全项目 (OWASP) 中的[核心规则集 (CRS) ](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 或 2.2.9。 WAF 会自动更新以包含针对新漏洞的保护，而无需其他配置。

![应用程序网关 WAF 示意图](./media/waf-overview/WAF1.png)

应用程序网关以应用程序传送控制器 (ADC) 的形式运行。 它提供安全套接字层 (SSL) 终止、基于 Cookie 的会话相关性、轮循负载分配、基于内容的路由，以及托管多个网站和安全增强功能的能力。

应用程序网关安全增强功能包括 SSL 策略管理和端到端 SSL 支持。 WAF 与应用程序网关集成，使应用程序的安全性得到增强。 这种组合可使 Web 应用程序免受常见漏洞的威胁。 此外，WAF 提供一个易于配置的中央位置用于管理应用程序。

## <a name="benefits"></a>优点

本部分介绍应用程序网关及其 WAF 提供的核心优势。

### <a name="protection"></a>保护

* 无需修改后端代码即可保护 Web 应用程序免受 Web 漏洞和攻击的威胁。

* 同时保护多个 Web 应用程序。 应用程序网关的实例可以托管的 web 应用程序防火墙保护的最多 100 个网站。

### <a name="monitoring"></a>监视

* 使用实时 WAF 日志监视 Web 应用程序受到的攻击。 该日志与 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) 集成，可用于跟踪 WAF 警报和轻松监视趋势。

* 应用程序网关 WAF 已与 Azure 安全中心集成。 在安全中心可以集中查看所有 Azure 资源的安全状态。

### <a name="customization"></a>自定义

* 可根据应用程序的要求自定义 WAF 规则和规则组，并消除误报。

## <a name="features"></a>功能

- SQL 注入防护。
- 跨站点脚本防护。
- 防范其他常见 Web 攻击，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含。
- 防范 HTTP 协议违规。
- 防范 HTTP 协议异常，例如缺少主机用户代理和接受标头。
- 防范机器人、爬网程序和扫描程序。
- 检测常见应用程序错误配置（例如 Apache 和 IIS）。
- 可配置请求大小的下限和上限。
- 使用排除列表可以忽略 WAF 评估中的某些请求属性。 常见示例是 Active Directory 插入的令牌，这些令牌用于身份验证或密码字段。

### <a name="core-rule-sets"></a>核心规则集

应用程序网关支持两个规则集：CRS 3.0 和 CRS 2.2.9。 这些规则可以防范 Web 应用程序中出现恶意活动。

应用程序网关 WAF 中默认已预先配置 CRS 3.0。 但你可以选择改用 CRS 2.2.9。 与 CRS 2.2.9 相比，CRS 3.0 的误报数更少。 还可以[根据需求自定义规则](application-gateway-customize-waf-rules-portal.md)。

WAF 可针对以下 Web 漏洞提供保护：

- SQL 注入攻击
- 跨站点脚本攻击
- 其他常见攻击，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含
- HTTP 协议违规
- HTTP 协议异常，例如缺少主机用户代理和接受标头
- 机器人、爬网程序和扫描程序
- 常见应用程序错误配置（例如 Apache 和 IIS）

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 包含下表中所示的 13 个规则组。 每个组包含多个可以禁用的规则。

|规则组|描述|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|锁定方法（PUT、PATCH）|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|防范端口和环境扫描程序|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|防范协议和编码问题|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|防范标头注入、请求走私和响应拆分|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|防范文件和路径攻击|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|防范远程文件包含 (RFI) 攻击|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|防范远程代码执行攻击|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|防范 PHP 注入攻击|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|防范跨站点脚本攻击|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|防范 SQL 注入攻击|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|防范会话固定攻击|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 包含下表中所示的 10 个规则组。 每个组包含多个可以禁用的规则。

|规则组|描述|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|防范协议违规（例如无效字符，或使用请求正文执行 GET）|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|防范错误的标头信息|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|防范参数或文件超出限制|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|防范受限的方法、标头和文件类型|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|防范 Web 爬网程序和扫描程序|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|防范常规攻击（例如会话固定、远程文件包含和 PHP 注入）|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|防范 SQL 注入攻击|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|防范跨站点脚本攻击|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|防范路径遍历攻击|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|防范后门特洛伊木马|

### <a name="waf-modes"></a>WAF 模式

应用程序网关 WAF 可配置为在以下两种模式中运行：

* **检测模式**：监视并记录所有威胁警报。 在“诊断”部分为应用程序网关启用日志记录诊断。 另外，必须确保已选择并启用 WAF 日志。 在检测模式下运行时，Web 应用程序防火墙不会阻止传入的请求。
* **防护模式**：阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权的访问”异常，且连接会终止。 防护模式会在 WAF 日志中记录此类攻击。

### <a name="anomaly-scoring-mode"></a>异常评分模式

OWASP 使用两种模式来确定是否阻止流量：传统模式和异常评分模式。

在传统模式下，将独立评估与任何规则匹配的流量，无论是否也匹配其他规则。 此模式很容易理解。 但是，它也存在一种限制：不知道有多少个规则与特定的请求相匹配。 因此，我们引入了异常评分模式。 这是 OWASP 3.*x* 中的默认模式。

在异常评分模式下，当防火墙处于防护模式时，不会立即阻止与任何规则匹配的流量。 规则采用特定的严重性：“严重”、“错误”、“警告”或“通知”。 该严重性会影响请求的数值（称为“异常评分”）。 例如，出现一个“警告”规则匹配项会生成评分值 3。 出现一个“严重”规则匹配项会生成评分值 5。

|Severity  |值  |
|---------|---------|
|严重     |5|
|错误        |4|
|警告      |3|
|注意       |2|

异常评分的阈值为 5，超过该阈值的流量将被阻止。 因此，出现一个“严重”规则匹配项就足以让应用程序网关 WAF 阻止请求，即使 WAF 处于防护模式。 但是，出现一个“警告”规则匹配项只会将异常评分增加 3，这并不足以阻止流量。

> [!NOTE]
> 当 WAF 规则与流量匹配时记录的消息包含操作值“Blocked”。 但实际上只阻止了异常评分已达到或超过 5 的流量。  

### <a name="waf-monitoring"></a>WAF 监视

监视应用程序网关的运行状况非常重要。 将 WAF 与 Azure 安全中心、Azure Monitor 和 Azure Monitor 日志集成后，可以监视 WAF 及其保护的应用程序的运行状况。

![应用程序网关 WAF 诊断示意图](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

应用程序网关日志与 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) 集成。 这样，便可以跟踪包括 WAF 警报和日志在内的诊断信息。 可以在门户上的应用程序网关资源中的“诊断”选项卡上访问此功能，或者直接通过 Azure Monitor 访问。 有关如何启用日志的详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

#### <a name="azure-security-center"></a>Azure 安全中心

[安全中心](../security-center/security-center-intro.md)可帮助你防止、 检测和响应威胁。 它提供增加的可见性并控制 Azure 资源的安全性。 应用程序网关[与安全中心集成](application-gateway-integration-security-center.md)。 安全中心会扫描环境以检测未受保护的 web 应用程序。 它可以建议应用程序网关 WAF 保护这些易受攻击的资源。 直接从安全中心创建防火墙。 这些 WAF 实例与安全中心集成。 它们向发送警报和运行状况信息安全中心进行报告。

![安全中心概述窗口](./media/waf-overview/figure1.png)

#### <a name="logging"></a>日志记录

应用程序网关 WAF 提供有关检测到的每个威胁的详细报告。 日志记录与 Azure 诊断日志相集成。 警报是以 .json 格式记录的。 这些日志可与 [Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)集成。

![应用程序网关诊断日志窗口](./media/waf-overview/waf2.png)

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

应用程序网关 WAF 在新的 SKU 下提供。 此 SKU 仅在 Azure 资源管理器预配模型中可用，在经典部署模型中不可用。 此外，WAF SKU 仅提供中型和大型应用程序网关实例大小。 应用程序网关的所有限制同样适用于 WAF SKU。

定价基于每小时网关实例费用和数据处理费用。 WAF SKU 的[应用程序网关定价](https://azure.microsoft.com/pricing/details/application-gateway/)不同于标准 SKU 费用。 数据处理费用相同。 不会按规则或规则组收费。 可以保护同一个 Web 应用程序防火墙后面的多个 Web 应用程序。 为多个应用程序提供支持不会产生费用。

## <a name="next-steps"></a>后续步骤

参阅[如何在应用程序网关上配置 Web 应用程序防火墙](tutorial-restrict-web-traffic-powershell.md)。
