---
title: Azure Web 应用程序防火墙（WAF） Geomatch 自定义规则
description: 本文概述了 Azure 应用程序关上的 Web 应用程序防火墙（WAF） geomatch 自定义规则。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: 6725e1de21dbd103850071f7511e2800c6bd7b69
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961083"
---
# <a name="geomatch-custom-rules-preview"></a>Geomatch 自定义规则（预览）

自定义规则允许你创建定制的规则，以满足应用程序和安全策略的确切需求。 现在，你可以按国家/地区限制对你的 web 应用程序的访问。 与所有自定义规则一样，此逻辑可以与其他规则结合，以满足应用程序的需求。

若要创建异地筛选自定义规则，只需选择 "*地理位置*" 作为 "匹配类型"，然后从应用程序中选择要允许/阻止的国家/地区。 有关详细信息，请参阅[如何在 Powershell 中创建自定义规则](configure-waf-custom-rules.md)和更多自定义规则示例（waf-rules.md）。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="country-codes"></a>国家/地区代码

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
