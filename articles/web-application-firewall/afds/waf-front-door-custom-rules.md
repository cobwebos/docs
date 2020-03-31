---
title: Azure 前门的 Web 应用程序防火墙自定义规则
description: 了解如何使用 Web 应用程序防火墙 （WAF） 自定义规则来保护 Web 应用程序免受恶意攻击。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475818"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>使用 Azure 前门的 Web 应用程序防火墙的自定义规则

使用前门的 Azure Web 应用程序防火墙 （WAF） 允许您根据定义的条件控制对 Web 应用程序的访问。 自定义 WAF 规则由优先级编号、规则类型、匹配条件和操作组成。 有两种类型的自定义规则：匹配规则和速率限制规则。 匹配规则基于一组匹配条件控制访问，而速率限制规则根据匹配条件和传入请求的速率控制访问。 您可以禁用自定义规则以防止对其进行计算，但仍保留配置。 

## <a name="priority-match-conditions-and-action-types"></a>优先级、匹配条件和操作类型

您可以使用自定义 WAf 规则来控制访问，该规则定义优先级编号、规则类型、匹配条件数组和操作。 

- **优先级：** 是描述 WAF 规则评估顺序的唯一整数。 优先级较低的规则在具有较高值的规则之前进行评估。 优先级编号在所有自定义规则中必须是唯一的。

- **操作：** 定义在 WAF 规则匹配时如何路由请求。 您可以选择以下操作之一，在请求与自定义规则匹配时应用。

    - *允许*- WAF 将任务转发到后端，在 WAF 日志和退出中记录条目。
    - *块*- 请求被阻止，WAF 向客户端发送响应，而不将请求转发到后端。 WAF 在 WAF 日志中记录条目。
    - *日志*- WAF 在 WAF 日志中记录条目，并继续评估下一个规则。
    - *重定向*- WAF 将请求重定向到指定的 URI，在 WAF 日志中记录条目，然后退出。

- **匹配条件：** 定义匹配变量、运算符和匹配值。 每个规则可能包含多个匹配条件。 匹配条件可能基于地理位置、客户端 IP 地址 （CIDR）、大小或字符串匹配。 字符串匹配可以针对匹配变量的列表。
  - **匹配变量：**
    - 请求方法
    - QueryString
    - PostArgs
    - 请求库里
    - RequestHeader
    - RequestBody
    - Cookie
  - **算子：**
    - 任何：通常用于定义默认操作（如果没有匹配规则）。 任何是所有运算符的匹配项。
    - 等于
    - 包含
    - 小于：大小约束
    - 大于：大小约束
    - 小于等于：大小约束
    - 大于ThanOr相等：大小约束
    - BeginsWith
    - EndsWith
    - 正则表达式
  
  - **Regex**不支持以下操作： 
    - 回引用和捕获子表达式
    - 任意零宽度断言
    - 子例程引用和递归模式
    - 条件模式
    - 回溯控制动词
    - \C 单字节指令
    - \R newline 匹配指令
    - 匹配重置指令的 #K 开始
    - 标注和嵌入式代码
    - 原子分组和占有限定符

  - **否定 [可选]：** 如果条件的结果应否定，则可以将*否定*条件设置为 true。
      
  - **转换 [可选]：** 在尝试匹配之前要执行的字符串列表。 这些转换可以是：
     - 大写 
     - 小写
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **匹配值：** 支持的 HTTP 请求方法值包括：
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - LOCK
     - UNLOCK
     - 配置文件
     - OPTIONS
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - 复制
     - MOVE

## <a name="examples"></a>示例

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>基于 http 参数的 WAF 自定义规则示例

下面是一个示例，该示例显示了具有两个匹配条件的自定义规则的配置。 请求来自引用者定义的指定站点，查询字符串不包含"密码"。

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
阻止"PUT"方法的示例配置如下所示：

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

可以生成自定义规则，该规则指定传入请求的一部分的大小约束。 例如，下面的规则阻止超过 100 个字符的 Url。

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
- [使用 Azure PowerShell 配置 Web 应用程序防火墙策略](waf-front-door-custom-rules-powershell.md) 
- 了解[前门的 Web 应用程序防火墙](afds-overview.md)
- 了解如何[创建 Front Door](../../frontdoor/quickstart-create-front-door.md)。

