---
title: 应用程序网关上的 Azure Web 应用程序防火墙（WAF） v2 自定义规则
description: 本文概述 Azure 应用程序 Gateway 上的 Web 应用程序防火墙（WAF） v2 自定义规则。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.openlocfilehash: 323f01e08007260d4fb6d651b20937c5d5d5e357
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645083"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Azure 应用程序网关上的 Web 应用程序防火墙 v2 的自定义规则

Azure 应用程序网关 Web 应用程序防火墙（WAF） v2 附带了一个预配置的平台托管的规则集，它可提供对多种不同类型的攻击的保护。 这些攻击包括跨站点脚本、SQL 注入和其他攻击。 如果你是 WAF 管理员，则可能想要编写自己的规则来补充核心规则集（CRS）规则。 规则可以根据匹配条件阻止或允许请求的流量。

自定义规则允许您创建自己的规则，这些规则将为通过 WAF 的每个请求进行评估。 这些规则的优先级高于托管规则集中的其余规则。 自定义规则包含规则名称、规则优先级和匹配条件的数组。 如果满足这些条件，则执行操作（允许或阻止）。

例如，可以阻止来自 192.168.5.4/24 范围内的 IP 地址的所有请求。 在此规则中，运算符为*IPMatch*，MATCHVALUES 是 IP 地址范围（192.168.5.4/24），操作是阻止流量。 还可以设置规则的名称和优先级。

自定义规则支持使用复合逻辑来创建更高级的规则来满足您的安全需求。 例如，（条件 1**和**条件2）**或**条件3）。  此示例表示如果满足条件 1**和**条件2，**或者**满足条件3，则 WAF 应采用自定义规则中指定的操作。

同一规则内的不同匹配条件始终使用**和**复合。 例如，阻止来自特定 IP 地址的流量，并且仅当它们使用特定的浏览器时才阻止。

如果你想要**或**两个不同的条件，则这两个条件必须属于不同规则。 例如，阻止来自特定 IP 地址的流量，或者阻止流量（如果它们使用特定的浏览器）。

> [!NOTE]
> WAF 自定义规则的最大数目为100。 有关应用程序网关限制的详细信息，请参阅[Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)。

自定义规则也支持正则表达式，就像 CRS 规则集一样。 有关这些示例的示例，请参阅[创建和使用自定义 web 应用程序防火墙规则](create-custom-waf-rules.md)中的示例3和5。

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

这是规则的名称。 此名称将出现在日志中。

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

- Allow –授权事务，跳过所有后续规则。 这意味着，指定的请求将添加到允许列表中，并且一旦匹配，请求将停止进一步评估并发送到后端池。 对于任何其他自定义规则或托管规则，不会评估允许列表上的规则。
- Block –阻止基于*SecDefaultAction* （检测/阻止模式）的事务。 就像 "允许" 操作一样，在对请求进行评估并将其添加到阻止列表后，评估将停止，请求会被阻止。 满足相同条件的任何请求都不会进行评估，只会被阻止。 
- Log –允许规则写入日志，但允许其余规则运行以进行评估。 后续自定义规则按优先级顺序进行计算，后跟托管规则。

## <a name="geomatch-custom-rules-preview"></a>Geomatch 自定义规则（预览）

自定义规则允许创建定制的规则，以满足应用程序的确切需求和安全策略。 现在，你可以按国家/地区限制对你的 web 应用程序的访问，该国家/地区提供公共预览版。 与所有自定义规则一样，此逻辑可以与其他规则结合，以满足应用程序的需求。 

   > [!NOTE]
   > Geomatch 自定义规则在美国中南部和北欧提供。 若要在门户中访问它们，请使用[此链接](https://aka.ms/AppGWWAFGeoMatch)，直到所有人都可以使用。 

如果使用的是 Geomatch 运算符，则选择器可以是以下两位数国家/地区代码中的任何一个。 

|国家/地区代码 | 国家/地区名称 |
| ----- | ----- |
| AD | 安道尔 |
| AE | 阿拉伯联合酋长国|
| AF | 阿富汗|
| AG | 安提瓜和巴布达|
| AL | 阿尔巴尼亚|
| AM | 亚美尼亚|
| AO | 安哥拉|
| AR | 阿根廷|
| AS | 美属萨摩亚|
| AT | 奥地利|
| AU | 澳大利亚|
| AZ | 阿塞拜疆|
| BA | 波斯尼亚和黑塞哥维那|
| BB | 巴巴多斯|
| BD | 孟加拉|
| BE | 比利时|
| BF | 布基纳法索|
| BG | 保加利亚|
| BH | 巴林|
| BI | 布隆迪|
| BJ | 贝宁|
| BL | 圣巴泰勒米|
| BN | 文莱|
| BO | 玻利维亚|
| BR | 巴西|
| BS | 巴哈马群岛|
| BT | 不丹|
| BW | 博茨瓦纳|
| BY | 白俄罗斯|
| BZ | 伯利兹|
| CA | 加拿大|
| CD | 刚果民主共和国|
| CF | 中非共和国|
| CH | 瑞士|
| CI | 科特迪瓦|
| CL | 智利|
| CM | 喀麦隆|
| CN | 中国|
| CO | 哥伦比亚|
| CR | 哥斯达黎加|
| CU | 古巴|
| CV | 佛得角|
| CY | 塞浦路斯|
| CZ | 捷克共和国|
| 德国 | 德国|
| DK | 丹麦|
| DO | 多米尼加共和国|
| DZ | 阿尔及利亚|
| EC | 厄瓜多尔|
| EE | 爱沙尼亚|
| EG | 埃及|
| ES | 西班牙|
| ET | 埃塞俄比亚|
| FI | 芬兰|
| FJ | 斐济|
| FM | 密克罗尼西亚联邦|
| FR | 法国|
| GB | 英国|
| GE | 格鲁吉亚|
| GF | 法属圭亚那|
| GH | 加纳|
| GN | 几内亚|
| GP | 瓜德罗普岛|
| GR | 希腊|
| GT | 危地马拉|
| GY | 圭亚那|
| HK | 香港特别行政区|
| HN | 洪都拉斯|
| HR | 克罗地亚|
| HT | 海地|
| HU | 匈牙利|
| ID | 印度尼西亚|
| IE | 爱尔兰|
| IL | 以色列|
| IN | 印度|
| IQ | 伊拉克|
| IR | 伊朗伊斯兰共和国|
| IS | 冰岛|
| IT | 意大利|
| JM | 牙买加|
| JO | 约旦|
| JP | 日本|
| KE | 肯尼亚|
| KG | 吉尔吉斯斯坦|
| KH | 柬埔寨|
| KI | 基里巴斯|
| KN | 圣基茨和尼维斯|
| KP | 朝鲜民主主义人民共和国|
| KR | 韩国|
| KW | 科威特|
| KY | 开曼群岛|
| KZ | 哈萨克斯坦|
| 拉丁美洲 | 老挝人民民主共和国|
| LB | 黎巴嫩|
| LI | 列支敦士登|
| LK | 斯里兰卡|
| LR | 利比里亚|
| LS | 莱索托|
| LT | 立陶宛|
| LU | 卢森堡|
| LV | 拉脱维亚|
| LY | 利比亚 |
| MA | 摩洛哥|
| MD | 摩尔多瓦共和国|
| MG | 马达加斯加岛|
| MK | 北马其顿|
| ML | 马里|
| MM | 缅甸|
| MN | 蒙古|
| MO | 澳门特别行政区|
| MQ | 马提尼克岛|
| MR | 毛利塔尼亚|
| MT | 马耳他|
| MV | 马尔代夫|
| MW | 马拉维|
| MX | 墨西哥|
| MY | 马来西亚|
| MZ | 莫桑比克|
| 不可用 | 纳米比亚|
| NE | 尼日尔|
| NG | 尼日利亚|
| NI | 尼加拉瓜|
| NL | 荷兰|
| 是 | 挪威|
| NP | 尼泊尔|
| NR | 瑙鲁|
| NZ | 新西兰|
| OM | 阿曼|
| PA | 巴拿马|
| PE | 秘鲁|
| PH | 菲律宾|
| PK | 巴基斯坦|
| PL | 波兰|
| PR | 波多黎各|
| PT | 葡萄牙|
| PW | 帕劳群岛|
| PY | 巴拉圭|
| QA | 卡塔尔|
| RE | 留尼汪|
| RO | 罗马尼亚|
| RS | 塞尔维亚|
| RU | 俄罗斯联邦|
| RW | 卢旺达|
| SA | 沙特阿拉伯|
| SD | 苏丹|
| SE | 瑞典|
| SG | 新加坡|
| SI | 斯洛文尼亚|
| SK | 斯洛伐克|
| SN | 塞内加尔|
| SO | 索马里|
| SR | 苏里南|
| SS | 南苏丹|
| SV | 萨尔瓦多|
| SY | 阿拉伯叙利亚共和国|
| SZ | 斯威士兰|
| TC | 特克斯和凯科斯群岛|
| TG | 多哥|
| TH | 泰国|
| TN | 突尼斯|
| TR | 土耳其|
| TT | 特立尼达和多巴哥|
| TW | 台湾|
| TZ | 坦桑尼亚联合共和国|
| UA | 乌克兰|
| UG | 乌干达|
| 美国 | 美国|
| UY | 乌拉圭|
| UZ | 乌兹别克斯坦|
| VC | 圣文森特和格林纳丁斯|
| VE | 委内瑞拉|
| VG | 英属维尔京群岛|
| VI | 美属维尔京群岛|
| VN | 越南|
| ZA | 南非|
| ZM | 赞比亚|
| ZW | 津巴布韦|

## <a name="next-steps"></a>后续步骤

了解自定义规则后，[创建自己的自定义规则](create-custom-waf-rules.md)。
