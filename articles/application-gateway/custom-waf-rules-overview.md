---
title: Azure Web 应用程序防火墙 (WAF) 自定义规则
description: 本文提供的 web 应用程序防火墙概述了 Azure 应用程序网关 (WAF) 自定义规则。
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: d35e1dc96f65e51ab14c4962d1824334cbdb1616
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752027"
---
# <a name="custom-rules-for-web-application-firewall"></a>Web 应用程序防火墙的自定义规则

Azure 应用程序网关 web 应用程序防火墙 (WAF) 附带了预配置、 平台托管规则集，可提供从许多不同类型的攻击的保护。 这些攻击包括跨站点脚本、 SQL 注入和其他人。 如果你是 WAF 管理员，你可能想要编写自己的规则，以增加核心规则集 (CRS) 规则。 你的规则可以阻止或允许根据匹配条件的请求的流量。

自定义规则，可创建您自己的规则，其计算结果为通过此 WAF 的每个请求。 这些规则中的托管规则集保存的优先级高于其他规则。 自定义规则包含一个规则名称、 规则优先级和匹配条件的数组。 如果满足这些条件，则不执行操作 （若要允许或阻止）。

例如，您可以阻止 IP 地址范围 192.168.5.4/24 中的所有请求。 此规则中的运算符是*IPMatch*，matchValues 是 IP 地址范围 (192.168.5.4/24)，该操作将阻止该流量。 您还可以设置规则的名称和优先级。

自定义规则支持使用复合逻辑以使你的安全需要该地址的更高级的规则。 例如，(条件 1**并**条件 2)**或**条件 3)。  此示例意味着，如果条件 1**并**满足条件 2，**或**WAF 如果满足条件 3，则应执行的自定义规则中指定的操作。

始终使用复杂中相同的规则不同的匹配条件**和**。 例如，阻止流量来自特定 IP 地址，且仅当他们使用某些浏览器。

如果想要**或**两个不同的条件，两个条件必须是在不同的规则。 例如，如果他们正在使用特定的浏览器阻止来自特定 IP 地址或阻止流量的流量。

> [!NOTE]
> 自定义 WAF 规则的最大数目为 100。 有关应用程序网关限制的详细信息，请参阅[Azure 订阅和服务限制、 配额和约束](../azure-subscription-service-limits.md#application-gateway-limits)。

正则表达式也支持自定义规则，就像在 CRS 规则集。 有关这些示例，请参阅示例 3 和 5 中的[创建和使用自定义 web 应用程序防火墙规则](create-custom-waf-rules.md)。

## <a name="allowing-vs-blocking"></a>允许与锁定

允许和阻止的流量是可以轻松地自定义规则。 例如，可以阻止来自范围内的 IP 地址的所有流量。 您可以进行另一个规则以允许流量，如果请求来自特定浏览器。

若要允许的内容，请确保`-Action`参数设置为**允许**。 若要阻止的内容，请确保`-Action`参数设置为**块**。

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

上一个`$BlockRule`映射到以下自定义规则在 Azure 资源管理器：

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

此自定义规则包含一个名称、 优先级、 操作和要执行的操作，必须满足的条件相匹配的数组。 有关这些字段的更多说明，请参阅以下字段说明。 有关示例自定义规则，请参阅[创建和使用自定义 web 应用程序防火墙规则](create-custom-waf-rules.md)。

## <a name="fields-for-custom-rules"></a>针对自定义规则的字段

### <a name="name-optional"></a>名称 [可选]

这是规则的名称。 此名称显示在日志中。

### <a name="priority-required"></a>优先级 [必需]

- 确定在评估规则的顺序。 较低的值，前面计算规则。
-必须是唯一自定义的所有规则。 将规则之前评估具有优先级 100 的规则，优先级为 200。

### <a name="rule-type-required"></a>规则类型 [必需]

目前，必须是**MatchRule**。

### <a name="match-variable-required"></a>匹配变量 [必需]

必须是一个变量：

- RemoteAddr – IP 地址/远程计算机连接的主机名
- RequestMethod – HTTP 请求方法 （GET、 POST、 PUT、 DELETE 等。）
- 查询字符串 – 在 URI 中的变量
- PostArgs – 在 POST 正文中发送的参数
- RequestUri – 的请求的 URI
- RequestHeaders – 的请求标头
- RequestBody – 请求正文
- RequestCookies – 请求的 Cookie

### <a name="selector-optional"></a>[可选] 选择器

介绍 matchVariable 集合的字段。 例如，如果 matchVariable RequestHeaders，选择器可能是在*用户代理*标头。

### <a name="operator-required"></a>运算符 [必需]

必须是以下运算符之一：

- IPMatch-匹配变量是时，才使用*RemoteAddr*
- Equals-输入是 MatchValue 相同
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- 正则表达式

### <a name="negate-condition-optional"></a>要求反的条件 [可选]

求反的当前状态。

### <a name="transform-optional"></a>转换 [可选]

尝试使用的转换，以匹配项前执行操作的名称的字符串的列表。 这些可以是以下转换：

- 小写
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>匹配值 [必需]

列表的值进行匹配，这可以被视为与*或*ed。 例如，它可能是 IP 地址或其他字符串。 这些值的格式取决于以前的运算符。

### <a name="action-required"></a>操作 [必需]

- 允许 – 授权的事务，正在跳过所有后续规则。 这意味着到允许列表和匹配，请求将停止进一步评估并发送到后端池后，添加指定的请求。 在允许列表规则不会评估所有进一步自定义规则或托管的规则。
- 块 – 阻止基于事务*SecDefaultAction* （检测/预防模式）。 就像允许操作后请求将计算并添加到阻止列表，计算已停止，并请求被阻塞。 后满足相同条件的任何请求将不计算，而只需将被阻止。 
- 日志 – 允许写入日志，该规则还可让其他规则进行评估。 后续的自定义规则按优先级，跟托管规则的顺序计算。

## <a name="next-steps"></a>后续步骤

了解如何自定义规则之后,[创建自定义规则](create-custom-waf-rules.md)。
