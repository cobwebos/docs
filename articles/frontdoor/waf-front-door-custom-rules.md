---
title: Web 应用程序防火墙自定义规则的 Azure 第一道防线
description: 了解如何使用 web 应用程序防火墙 (WAF) 自定义规则保护 web 应用程序免受恶意攻击。
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459702"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>自定义规则为 web 应用程序防火墙与 Azure 第一道防线的
第一道防线服务的 azure web 应用程序防火墙 (WAF) 可控制对 web 应用程序基于你定义的条件访问。 自定义 WAF 规则组成优先级编号、 规则类型、 匹配条件和操作。 有两种类型的自定义规则： 与规则匹配和速率限制规则。 匹配规则控制访问权限基于匹配条件时的速率限制规则控制访问基于匹配条件和传入的请求的速率。 也可以禁用自定义规则，以防止它计算的但仍保留配置。 本文介绍了基于 http 参数的匹配规则。

## <a name="priority-match-conditions-and-action-types"></a>优先级、 匹配条件和操作类型
您可以控制自定义 WAf 规则，以定义优先级编号、 规则类型、 匹配条件和操作的访问。 

- **优先级：** 是一个唯一的整数，描述的 WAF 规则的计算顺序。 在具有较高的值的规则之前评估具有较低值的规则

- **操作：** 定义如何路由请求，如果 WAF 规则匹配。 您可以选择其中一个的以下操作时要应用某个请求与匹配的自定义规则。

    - *允许*的 WAF 将转发到后端 quest、 WAF 日志和退出中记录一个条目。
    - *块*-已阻止请求，WAF 而无需将请求转发到后端发送到客户端的响应。 WAF 在 WAF 日志中记录一个条目。
    - *日志*-WAF 日志中 WAF 的条目记录并将继续评估下一规则。
    - *重定向*的 WAF 将请求重定向到指定的 URI，在 WAF 日志中记录一个条目并退出。

- **匹配条件：** 定义匹配变量、 运算符和值匹配。 每个规则可能包含多个匹配条件。 匹配条件可以基于如下*匹配变量*:
    - RemoteAddr (客户端 IP)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **运算符：** 列表包括以下：
    - 任何一个： 通常用于定义默认操作，如果没有规则进行匹配。 任何是所有运算符的匹配项。
    - IPMatch： 定义 RemoteAddr 变量的 IP 限制
    - GeoMatch： 定义地理筛选 RemoteAddr 变量
    - 等于
    - Contains
    - LessThan： 大小限制
    - GreaterThan： 大小限制
    - LessThanOrEqual： 大小限制
    - GreaterThanOrEqual： 大小限制
    - BeginsWith
     - EndsWith

可以设置*negate*条件为 true，如果条件的结果应进行求反。

*与值匹配*定义可能的匹配值的列表。
支持的 HTTP 请求方法的值包括：
- GET
- 发布
- PUT
- HEAD
- DELETE
- 锁
- 解锁
- 配置文件
- OPTIONS
- PROPFIND
- PROPPATCH
- MKCOL
- 复制
- 移动

## <a name="examples"></a>示例

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>基于 http 参数 WAF 自定义规则示例

下面是一个示例，演示具有两个匹配条件的自定义规则的配置。 请求是从指定的站点定义的引用网站，并且查询字符串不包含"password"。

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
用于阻止"PUT"方法的示例配置所示如下所示：

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>大小限制

可能会生成一个自定义规则，指定传入请求的一部分的大小限制。 例如，以下规则将阻止长度超过 100 个字符的 Url。

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>后续步骤
- 了解有关[web 应用程序防火墙](waf-overview.md)
- 了解如何[创建 Front Door](quickstart-create-front-door.md)。

