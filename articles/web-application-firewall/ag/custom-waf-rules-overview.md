---
title: 应用程序网关上的 Azure Web 应用程序防火墙（WAF） v2 自定义规则
description: 本文概述 Azure 应用程序 Gateway 上的 Web 应用程序防火墙（WAF） v2 自定义规则。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 9d9deca0365e13a0a8ad7404a476b05d0afef077
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935001"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Azure 应用程序网关上的 Web 应用程序防火墙 v2 的自定义规则

Azure 应用程序网关 Web 应用程序防火墙（WAF） v2 附带了一个预配置的平台托管的规则集，它可提供对多种不同类型的攻击的保护。 这些攻击包括跨站点脚本、SQL 注入和其他攻击。 如果你是 WAF 管理员，则可能想要编写自己的规则来补充核心规则集（CRS）规则。 规则可以根据匹配条件阻止或允许请求的流量。

自定义规则允许您创建自己的规则，这些规则将为通过 WAF 的每个请求进行评估。 这些规则的优先级高于托管规则集中的其余规则。 自定义规则包含规则名称、规则优先级和匹配条件的数组。 如果满足这些条件，则执行操作（允许或阻止）。

例如，可以阻止来自 192.168.5.4/24 范围内的 IP 地址的所有请求。 在此规则中，运算符为*IPMatch*，MATCHVALUES 是 IP 地址范围（192.168.5.4/24），操作是阻止流量。 还可以设置规则的名称和优先级。

自定义规则支持使用复合逻辑来创建更高级的规则来满足您的安全需求。 例如，（条件 1**和**条件2）**或**条件3）。 这意味着，如果满足条件 1**和**条件2，**或者**满足条件3，则 WAF 应采用自定义规则中指定的操作。

同一规则内的不同匹配条件始终使用**和**复合。 例如，阻止来自特定 IP 地址的流量，并且仅当它们使用特定的浏览器时才阻止。

如果你想要**或**两个不同的条件，则这两个条件必须属于不同规则。 例如，阻止来自特定 IP 地址的流量，或者阻止流量（如果它们使用特定的浏览器）。

> [!NOTE]
> WAF 自定义规则的最大数目为100。 有关应用程序网关限制的详细信息，请参阅[Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)。

自定义规则也支持正则表达式，就像 CRS 规则集一样。 有关示例，请参阅[创建和使用自定义 web 应用程序防火墙规则](create-custom-waf-rules.md)中的示例3和5。

## <a name="allowing-vs-blocking"></a>允许与阻止

通过自定义规则，允许和阻止流量非常简单。 例如，可以阻止来自某个 IP 地址范围的所有流量。 如果请求来自特定的浏览器，则可以创建另一个规则以允许流量。

若要允许某些操作，请确保将 `-Action` 参数设置为 "**允许**"。 若要阻止某些操作，请确保将 `-Action` 参数设置为**block**。

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

上一个 `$BlockRule` 映射到 Azure 资源管理器中的以下自定义规则：

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

此自定义规则包含一个名称、优先级、操作以及要发生的操作必须满足的匹配条件的数组。 有关这些字段的进一步说明，请参阅以下字段说明。 有关自定义规则的示例，请参阅[创建和使用自定义 web 应用程序防火墙规则](create-custom-waf-rules.md)。

## <a name="fields-for-custom-rules"></a>自定义规则字段

### <a name="name-optional"></a>名称 [可选]

规则的名称。  它将出现在日志中。

### <a name="priority-required"></a>Priority [必需]

- 确定规则求的顺序。 值越低，规则的计算结果越低。 允许的范围为1-100。 
- 在所有自定义规则中必须是唯一的。 优先级为40的规则在优先级为80的规则之前计算。

### <a name="rule-type-required"></a>规则类型 [必需]

目前，必须为**MatchRule**。

### <a name="match-variable-required"></a>Match 变量 [必需]

必须是以下变量之一：

- RemoteAddr –远程计算机连接的 IP 地址/主机名
- RequestMethod – HTTP 请求方法（GET、POST、PUT、DELETE 等。）
- QueryString – URI 中的变量
- PostArgs –在 POST 正文中发送的参数。 仅当 "Content-type" 标头设置为 "application/x-url 编码" 和 "多部分/窗体数据" 时，才应用使用此匹配变量的自定义规则。
- RequestUri –请求的 URI
- RequestHeaders –请求的标头
- RequestBody –其中包含整个请求正文。 仅当 "Content-type" 标头设置为 "application/x-url 编码" 时，才应用使用此匹配变量的自定义规则。 
- RequestCookies –请求的 Cookie

### <a name="selector-optional"></a>选择器 [可选]

描述 matchVariable 集合的字段。 例如，如果 matchVariable 为 RequestHeaders，则选择器可能位于*用户代理*标头。

### <a name="operator-required"></a>运算符 [必需]

必须是下列运算符之一：

- IPMatch-仅当 Match 变量为*RemoteAddr*时使用
- 等于–输入与 MatchValue 相同
- 包含
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- 正则表达式
- Geomatch （预览版）

### <a name="negate-condition-optional"></a>否定条件 [可选]

对当前条件求反。

### <a name="transform-optional"></a>转换 [可选]

在尝试匹配之前，包含转换名称的字符串的列表。 这些转换可以是以下转换：

- 小写
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>匹配值 [必需]

要匹配的值的列表，可被视为*或*。 例如，它可以是 IP 地址或其他字符串。 值格式取决于上一个运算符。

### <a name="action-required"></a>操作 [必需]

- Allow –授权事务，并跳过所有其他规则。 指定的请求将添加到允许列表中，一旦匹配，请求将停止进一步评估并发送到后端池。 对于任何其他自定义规则或托管规则，不会评估允许列表上的规则。
- Block –阻止基于*SecDefaultAction* （检测/阻止模式）的事务。 就像 "允许" 操作一样，在对请求进行评估并将其添加到阻止列表后，评估将停止，请求会被阻止。 满足相同条件的任何请求都不会进行评估，只会被阻止。 
- Log –允许规则写入日志，但允许其余规则运行以进行评估。 其他自定义规则按优先级顺序进行计算，后跟托管规则。

## <a name="geomatch-custom-rules-preview"></a>Geomatch 自定义规则（预览）

自定义规则使你可以创建定制的规则，以满足应用程序和安全策略的确切需求。 你可以按国家/地区限制对你的 web 应用程序的访问。 有关详细信息，请参阅[Geomatch 自定义规则（预览版）](geomatch-custom-rules.md)。

## <a name="next-steps"></a>后续步骤

了解自定义规则后，[创建自己的自定义规则](create-custom-waf-rules.md)。
