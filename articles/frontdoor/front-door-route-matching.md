---
title: Azure 前端-路由规则匹配监视 |Microsoft Docs
description: 本文可帮助你了解 Azure 前门如何匹配用于传入请求的路由规则
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 67940db973f494cd4a12c2f16db528e0b113d656
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449207"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>如何将请求匹配到路由规则

建立连接并完成 TLS 握手后，当请求落在前门环境中时，前一种情况下，首先需要确定要将请求与哪个特定路由规则进行匹配，然后在配置中采用定义的操作。 以下文档介绍了 Front Door 如何确定在处理 HTTP 请求时要使用的路由配置。

## <a name="structure-of-a-front-door-route-configuration"></a>Front Door 路由配置结构
Front Door 传递规则配置由两个主要部分组成：“左侧”和“右侧”。 我们将传入的请求与路由的左侧相匹配，而右侧则定义我们处理请求的方式。

### <a name="incoming-match-left-hand-side"></a>传入的匹配项（左侧）
以下属性确定传入的请求是否与传递规则（或左侧）匹配：

* **** HTTP 协议 (HTTP/HTTPS)
* **宿主** (例如 www \. foo.com \* bar.com) 
* **** 路径（例如，/\*、/users/\*/file.gif）

这些属性会在内部扩展，因此协议/主机/路径的每种组合都是潜在的匹配集。

### <a name="route-data-right-hand-side"></a>路由数据（右侧）
决定如何处理请求取决于是否为特定路由启用了缓存。 因此，如果没有针对请求的缓存应答，我们会将请求转发到配置的后端池中的相应后端。

## <a name="route-matching"></a>路由匹配
本节将重点介绍我们如何匹配给定的 Front Door 传递规则。 基本概念是始终优先匹配最具体的匹配项，仅查看“左侧”****。  首先基于 HTTP 协议匹配，接下来是前端主机，然后是路径。

### <a name="frontend-host-matching"></a>前端主机匹配
匹配前端主机时，使用以下定义的逻辑：

1. 查找主机上具有完全匹配项的任何路由。
2. 如果没有精确的前端主机匹配项，则拒绝该请求并发送“400 错误请求”错误。

为进一步解释此过程，让我们看看 Front Door 路由的示例配置（仅限左侧）：

| 路由规则 | 前端主机 | 路径 |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C | www \. fabrikam.com，foo.adventure-works.com  | /\*, /images/\* |

如果以下传入的请求已发送到 Front Door，则它们将与上面的传递规则匹配：

| 传入前端主机 | 匹配的传递规则 |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www \. fabrikam.com | C |
| images.fabrikam.com | 错误 400：错误请求 |
| foo.adventure-works.com | C |
| contoso.com | 错误 400：错误请求 |
| www \. adventure-works.com | 错误 400：错误请求 |
| www \. northwindtraders.com | 错误 400：错误请求 |

### <a name="path-matching"></a>路径匹配
确定特定前端主机并将可能的传递规则过筛选到仅具有该前端主机的路由后，Front Door 会根据请求路径筛选传递规则。 使用与前端主机类似的逻辑：

1. 在路径上查找具有完全匹配项的任何传递规则
2. 如果没有完全匹配的路径，请查找具有匹配的通配符路径的传递规则
3. 如果找不到具有匹配路径的传递规则，则拒绝该请求并返回“400：错误请求”错误 HTTP 应答。

>[!NOTE]
> 任何没有通配符的路径都视作完全匹配的路径。 即使路径以斜线结尾，仍被视作完全匹配。

为了进一步说明，让我们看看另一组示例：

| 路由规则 | 前端主机    | 路径     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

对于该配置，将产生以下匹配表示例：

| 传入的请求    | 匹配的路由 |
|---------------------|---------------|
| www \. contoso.com/            | A             |
| www \. contoso.com/a           | B             |
| www \. contoso.com/ab          | C             |
| www \. contoso.com/abc         | D             |
| www \. contoso.com/abzzz       | B             |
| www \. contoso.com/abc/        | E             |
| www \. contoso.com/abc/d       | F             |
| www \. contoso.com/abc/def     | G             |
| www \. contoso.com/abc/defzzz  | F             |
| www \. contoso.com/abc/def/ghi | F             |
| www \. contoso.com/path        | B             |
| www \. contoso.com/path/       | H             |
| www \. contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> 如果完全匹配的前端主机没有包含全部路由路径 (`/*`) 的传递规则，则不会与任何传递规则匹配。
>
> 示例配置：
>
> | 路由 | 主机             | 路径    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> 匹配表：
>
> | 传入的请求       | 匹配的路由 |
> |------------------------|---------------|
> | profile.domain.com/other | 无。 错误 400：错误请求 |

### <a name="routing-decision"></a>路由决策
匹配了单个 Front Door 路由规则之后，就需要选择如何处理请求。 对于匹配的传递规则，如果 Front Door 具有可用的缓存应答，则将相同的服务返回给客户端。 否则，下一步就需要评估是否为匹配的传递规则配置了 [URL 重写（自定义转发路径）](front-door-url-rewrite.md)。 如果未定义自定义转发路径，则请求将按原样转发到配置的后端池中的相应后端。 另外，系统会按照定义的[自定义转发路径](front-door-url-rewrite.md)更新请求路径，然后将其转发到后端。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
