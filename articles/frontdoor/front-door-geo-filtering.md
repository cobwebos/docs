---
title: 在 Azure Front Door 服务的域中的地区筛选 | Microsoft Docs
description: 本文介绍 Azure Front Door 服务的地区筛选策略
services: frontdoor
documentationcenter: ''
author: KumudD
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: a7b4975a81c0966e5cbff0c8b940c9231e66f32b
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407634"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door"></a>什么是 Azure Front Door 的域中的地区筛选？

默认情况下，无论发出请求的用户位于哪里，Azure Front Door 服务都会响应用户请求。 但是，在某些情况下，可能需要按国家/地区限制对 Web 应用程序的访问。 Front Door 上的 Web 应用程序防火墙 (WAF) 服务使你可以使用终结点上特定路径的自定义访问规则来定义策略，以允许或阻止来自指定国家/地区的访问。 

WAF 策略通常包括一组自定义规则。 规则由匹配条件、操作和优先级组成。 在匹配条件中，请定义匹配变量、运算符和匹配值。  就地区筛选规则来说，匹配变量为 REMOTE_ADDR，运算符为 GeoMatch，值为感兴趣的双字母国家/地区代码。 可以将 GeoMatch 条件与 REQUEST_URI 字符串匹配条件组合在一起，创建一个基于路径的地区筛选规则。

可以通过 [Azure PowerShell](front-door-tutorial-geo-filtering.md) 或[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)为 Front Door 配置地区筛选策略。

## <a name="country-code-reference"></a>国家/地区代码参考

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
| BS | 巴哈马|
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
| DE | 德国|
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
| LA | 老挝人民民主共和国|
| LB | 黎巴嫩|
| LI | 列支敦士登|
| LK | 斯里兰卡|
| LR | 利比里亚|
| LS | 莱索托|
| LT | 立陶宛|
| LU | 卢森堡|
| LV | 拉脱维亚|
| LY | 利比亚|
| MA | 摩洛哥|
| MD | 摩尔多瓦共和国|
| MG | 马达加斯加岛|
| MK | 马其顿|
| ML | 马里|
| MM | 缅甸|
| MN | 蒙古|
| MO | 中国澳门|
| MQ | 马提尼克岛|
| MR | 毛利塔尼亚|
| MT | 马耳他|
| MV | 马尔代夫|
| MW | 马拉维|
| MX | 墨西哥|
| MY | 马来西亚|
| MZ | 莫桑比克|
| NA | 纳米比亚|
| NE | 尼日尔|
| NG | 尼日利亚|
| NI | 尼加拉瓜|
| NL | 荷兰|
| 否 | 挪威|
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

- 了解[通过 Front Door 实现的应用程序层安全性](front-door-application-security.md)。
- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
