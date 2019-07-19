---
title: Azure 前门的 Web 应用程序防火墙自定义规则
description: 了解如何使用 web 应用程序防火墙 (WAF) 自定义规则来保护 web 应用程序免受恶意攻击。
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 02b335de7f105d768168d5f798ec9109136d7430
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846262"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>适用于 Azure 前门的 web 应用程序防火墙的自定义规则
使用带有前门服务的 Azure web 应用程序防火墙 (WAF), 可以根据定义的条件来控制对 web 应用程序的访问。 自定义 WAF 规则由优先级编号、规则类型、匹配条件和操作组成。 自定义规则有两种类型: 匹配规则和速率限制规则。 匹配规则根据匹配条件控制访问, 而速率限制规则根据匹配条件和传入请求的速率控制访问。 您可以禁用自定义规则以防止对其进行评估, 但仍保留配置。 本文介绍了基于 http 参数的匹配规则。

## <a name="priority-match-conditions-and-action-types"></a>优先级、匹配条件和操作类型
你可以使用自定义 WAf 规则来控制访问权限, 该规则定义了优先级编号、规则类型、匹配条件和操作。 

- **Priority:** 是描述 WAF 规则的计算顺序的唯一整数。 在值较高的规则之前计算具有较低值的规则

- **操作:** 定义在匹配 WAF 规则时如何路由请求。 当请求与自定义规则匹配时, 可以选择要应用的以下操作之一。

    - *允许*-WAF 将该寻找转发到后端, 并将条目记录到 WAF 日志中并退出。
    -  阻止请求被阻止, WAF 向客户端发送响应, 而不将请求转发到后端。 WAF 记录 WAF 日志中的条目。
    -  WAF 记录 WAF 日志中的条目, 并继续评估下一规则。
    - *重定向*-WAF 将请求重定向到指定的 URI, 将条目记录到 WAF 日志中并退出。

- **Match 条件:** 定义匹配变量、运算符和匹配值。 每个规则可能包含多个匹配条件。 匹配条件可以基于以下*匹配变量*:
    - RemoteAddr (客户端 IP)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operator:** list 包括以下内容:
    - 如果没有匹配的规则, 则 "所有:" 通常用于定义默认操作。 Any 是 match all 运算符。
    - IPMatch: 定义 RemoteAddr 变量的 IP 限制
    - GeoMatch: 定义 RemoteAddr 变量的地域筛选
    - 等于
    - Contains
    - LessThan: size 约束
    - GreaterThan: size 约束
    - LessThanOrEqual: size 约束
    - GreaterThanOrEqual: size 约束
    - 开头为
     - EndsWith

如果条件的结果应为 "求反", 则可以将 "*否定*条件" 设置为 true。

*Match 值*定义可能的匹配值的列表。
支持的 HTTP 请求方法值包括:
- GET
- 发布
- PUT
- HEAD
- DELETE
- 住
- 解锁
- 简介
- OPTIONS
- PROPFIND
- PROPPATCH
- MKCOL
- 复本
- 移动

## <a name="examples"></a>示例

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>基于 http 参数的 WAF 自定义规则示例

下面的示例演示具有两个匹配条件的自定义规则的配置。 请求来自于引用方定义的指定站点, 并且查询字符串不包含 "password"。

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
阻止 "PUT" 方法的示例配置如下所示:

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

### <a name="size-constraint"></a>大小约束

你可以构建自定义规则, 用于指定对传入请求的部分的大小约束。 例如, 下面的规则阻止长度超过100个字符的 Url。

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
- 了解[web 应用程序防火墙](waf-overview.md)
- 了解如何[创建 Front Door](quickstart-create-front-door.md)。

