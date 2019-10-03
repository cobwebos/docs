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
ms.openlocfilehash: 344e04985c52945b2917d3b5f616d5fca6051ab9
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839773"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>适用于 Azure 前门的 web 应用程序防火墙的自定义规则
使用带有前门服务的 Azure web 应用程序防火墙 (WAF), 可以根据定义的条件来控制对 web 应用程序的访问。 自定义 WAF 规则由优先级编号、规则类型、匹配条件和操作组成。 自定义规则有两种类型: 匹配规则和速率限制规则。 匹配规则根据一组匹配条件控制访问权限, 而速率限制规则根据匹配条件和传入请求的速率控制访问。 您可以禁用自定义规则以防止对其进行评估, 但仍保留配置。 

## <a name="priority-match-conditions-and-action-types"></a>优先级、匹配条件和操作类型
你可以使用自定义的 WAf 规则来控制访问权限, 该规则定义了优先级编号、规则类型、匹配条件的数组和操作。 

- **Priority:** 是描述 WAF 规则的计算顺序的唯一整数。 具有较低优先级值的规则先于值较高的规则进行评估。 所有自定义规则中的优先级编号必须是唯一的。

- **操作:** 定义在匹配 WAF 规则时如何路由请求。 当请求与自定义规则匹配时, 可以选择要应用的以下操作之一。

    - *允许*-WAF 将该寻找转发到后端, 并将条目记录到 WAF 日志中并退出。
    - 阻止请求被阻止, WAF 向客户端发送响应, 而不将请求转发到后端。 WAF 记录 WAF 日志中的条目。
    - WAF 记录 WAF 日志中的条目, 并继续评估下一规则。
    - *重定向*-WAF 将请求重定向到指定的 URI, 将条目记录到 WAF 日志中并退出。

- **Match 条件:** 定义匹配变量、运算符和匹配值。 每个规则可能包含多个匹配条件。 匹配条件可以基于地理位置、客户端 IP 地址 (CIDR)、大小或字符串匹配。 字符串匹配可用于匹配变量列表。
  - **匹配变量:**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody
    - Cookie
  - **操作员**
    - 如果没有匹配的规则, 则 "所有:" 通常用于定义默认操作。 Any 是 match all 运算符。
    - 相等
    - 包含
    - LessThan: size 约束
    - GreaterThan: size 约束
    - LessThanOrEqual: size 约束
    - GreaterThanOrEqual: size 约束
    - 开头为
    - EndsWith
    - 正则表达式
  
  - **正则表达式**不支持以下操作: 
    - 反向引用和捕获子表达式
    - 任意零宽度断言
    - 子程序引用和递归模式
    - 条件模式
    - 回溯控制谓词
    - \C 单字节指令
    - \R 换行符 match 指令
    - \K start of match reset 指令
    - 标注和嵌入代码
    - 原子分组和所有格限定符

  - **取反 [optional]:** 如果条件的结果应为 "求反", 则可以将 "*否定*条件" 设置为 true。
      
  - **转换 [可选]:** 在尝试匹配之前, 包含转换名称的字符串的列表。 这些转换可以是以下转换:
     - 大写 
     - 小写
     - 修整
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **匹配值:** 支持的 HTTP 请求方法值包括:
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

