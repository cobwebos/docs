---
title: Azure web 应用程序防火墙（WAF） v2 自定义规则
description: 本文概述了 Azure 应用程序网关中的 web 应用程序防火墙（WAF） v2 自定义规则。
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 8ae5c9b6b52ea13e3d0981664e8c920cc5b47a01
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263562"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>概述：Web 应用程序防火墙 v2 的自定义规则

Azure 应用程序网关 web 应用程序防火墙（WAF） v2 附带了一个预配置的平台托管的规则集，它可提供对多种不同类型的攻击的保护。 这些攻击包括跨站点脚本、SQL 注入和其他攻击。 如果你是 WAF 管理员，则可能想要编写自己的规则以增加核心规则集规则。 你的规则可以根据匹配条件阻止或允许请求的流量。

使用自定义规则，你可以创建自己的规则，这些规则是通过 WAF 传递的每个请求进行评估的。 这些规则的优先级高于托管规则集中的其他规则。 自定义规则包含规则名称、规则优先级和匹配条件的数组。 如果满足这些条件，则执行操作以允许或阻止。

例如，可以创建一个规则来阻止来自 192.168.5.4/24 范围内的 IP 地址的所有请求。 在此规则中，运算符为*IPMatch*， *matchValues*是 IP 地址范围（192.168.5.4/24），*操作*是阻止流量。 还需要设置规则的名称和优先级。

自定义规则支持使用复合逻辑创建更高级的规则来解决安全需求。 例如，"（条件 1*和*条件2）*或*条件 3" 表示满足条件 1*和*条件2，*或*如果满足条件3，则 WAF 应执行在自定义规则中指定的操作。

同一规则内的不同匹配条件始终使用*和*复合。 例如，使用*和*的规则可能指定阻止来自特定 IP 地址的流量，并且仅当某个浏览器正在使用时使用。

如果要使用*或*两个不同的条件，则这两个条件必须属于不同规则。 例如，如果使用了特定的浏览器，则使用*或*的规则可能会指定阻止来自特定 IP 地址或阻止流量的流量。

> [!NOTE]
> WAF 自定义规则的最大数目为 100。 有关应用程序网关限制的详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md#application-gateway-limits)。

自定义规则也支持正则表达式，就像它们位于核心规则集内一样。 有关这些规则的示例，请参阅[创建和使用自定义 web 应用程序防火墙规则](create-custom-waf-rules.md)中的 "示例 3" 和 "示例 5"。

## <a name="allowing-or-blocking-traffic"></a>允许或阻止流量

通过自定义规则，允许或阻止流量很简单。 例如，可以阻止来自某个 IP 地址范围的所有流量。 如果请求来自某个浏览器，则可以创建另一个规则以允许流量。

若要允许某种流量，请确保将 `-Action` 参数设置为 **Allow**。 若要阻止某些操作，请确保将 `-Action` 参数设置为**block**，如以下代码所示：

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

前面 `$BlockRule` 映射到 Azure 资源管理器中的以下自定义规则：

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

此自定义规则包含一个名称、优先级、操作以及要执行操作必须满足的匹配条件的数组。 有关自定义规则字段的说明，请参阅以下各节。 有关自定义规则的示例，请参阅[创建和使用自定义 web 应用程序防火墙规则](create-custom-waf-rules.md)。

## <a name="custom-rule-fields"></a>自定义规则字段

### <a name="name-optional"></a>名称（可选）

这是规则的名称。 名称将显示在日志中。

### <a name="priority-required"></a>优先级（必需）

- 优先级确定规则的评估顺序。 值越小，规则的评估顺序越靠前。 允许的范围是从1到100。
- 优先级在所有自定义规则中必须是唯一的。 优先级为40的规则先于优先级为80的规则进行评估。

### <a name="rule-type-required"></a>规则类型（必需）

目前，规则类型必须为**MatchRule**。

### <a name="match-variable-required"></a>Match 变量（必需）

Match 变量必须是下列项之一：

- RemoteAddr:远程计算机连接的 IP 地址或主机名
- RequestMethod:HTTP 请求方法（GET、POST、PUT、DELETE 等）。
- 字符串URI 中的变量。
- PostArgs:在 POST 正文中发送的参数。 仅当 Content-type 标头设置为 "application/x-url 编码" 和 "多部分/窗体数据" 时，才应用使用此匹配变量的自定义规则。
- RequestUri请求的 URI。
- RequestHeaders请求的标头。
- RequestBody包含整个请求正文的变量。 仅当 Content-type 标头设置为 "application/x-url 编码" 时，才应用使用此匹配变量的自定义规则。 
- RequestCookies:请求的 cookie。

### <a name="selector-optional"></a>选择器（可选）

选择器描述 matchVariable 集合的字段。 例如，如果 matchVariable 为 "RequestHeaders"，则选择器可能位于用户代理标头。

### <a name="operator-required"></a>运算符（必需）

操作员必须是下列各项之一：

- IPMatch:仅当 match 变量为*RemoteAddr*时，才使用此运算符。
- 对应输入与 MatchValue 相同。
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- 正则表达式

### <a name="negate-condition-optional"></a>求反条件（可选）

对当前条件求反。

### <a name="transform-optional"></a>转换（可选）

一个字符串列表，其中包含要在尝试匹配之前完成的转换的名称。 转换包括：

- 小写
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>匹配值（必需）

MatchValues 字段是要匹配的值的列表，可将其视为*或*。 例如，值可以是 IP 地址或其他字符串。 值的格式取决于上一个运算符。

### <a name="action-required"></a>操作（必需）

"操作" 字段提供以下选项： 

- 允许：授权事务，并跳过所有后续规则。 这意味着，指定的请求将添加到允许列表中，并且在匹配后，请求将停止进一步评估并发送到后端池。 对于进一步的自定义规则或托管规则，不会评估允许列表上的规则。

- 阻止：基于*SecDefaultAction*阻止事务（检测/阻止模式）。 与允许操作类似，计算请求并将其添加到阻止列表后，将停止评估并阻止请求。 不会评估满足相同条件的后续请求。 它们只会被阻止。 

- 日志允许规则将写入日志，并允许其余规则运行以进行评估。 后续的自定义规则将接在托管规则的后面按优先顺序进行评估。

## <a name="next-steps"></a>后续步骤

现在，你已了解自定义规则，你可以[创建自己的自定义规则](create-custom-waf-rules.md)。
