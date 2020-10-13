---
title: Azure Front Door 体系结构和术语的规则引擎
description: 本文概述了 Azure Front Door 规则引擎功能。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/29/2020
ms.author: duau
ms.openlocfilehash: 8e478cebcf8c5c9365100ade23c3d610c24930ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569741"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>什么是 Azure Front Door 的规则引擎？ 

可以通过规则引擎自定义在边缘处理 HTTP 请求的方式，并对 Web 应用程序行为提供更多控制。 Azure Front Door 的规则引擎包含几个关键功能，例如：

* 强制执行 HTTPS，确保所有最终用户都能够通过安全连接与内容进行交互。
* 实施安全性标头，以防止基于浏览器的漏洞攻击，例如 HTTP Strict-Transport-Security (HSTS)、X-XSS-Protection、Content-Security-Policy、X-Frame-Options 以及用于跨域资源共享 (CORS) 方案的 Access-Control-Allow-Origin 标头。 基于安全性的属性也可以用 Cookie 来定义。
* 基于请求标头内容、Cookie 或查询字符串的模式，将请求路由到应用程序的移动或桌面版本。
* 使用重定向功能将 301、302、307 和 308 重定向返回到客户端，以定向到新的主机名、路径或协议。
- 基于传入请求动态修改路由的缓存配置。
- 重写请求 URL 路径，并将请求转接到配置的后端池中的相应后端。

## <a name="architecture"></a>体系结构 

规则引擎在边缘处理请求。 当请求到达 Front Door 终结点时，会首先执行 WAF，然后执行与前端/域关联的规则引擎配置。 执行规则引擎配置意味着父路由规则已经是匹配项。 要执行每个规则中的所有操作，必须满足规则中的所有匹配条件。 如果请求与规则引擎配置中的任何条件都不匹配，则会执行默认路由规则。 

例如，在下图中，规则引擎配置为追加响应标头。 如果满足匹配条件，则标头将更改缓存控件的最大期限。 

![响应标头操作](./media/front-door-rules-engine/rules-engine-architecture-3.png)

在另一个示例中，我们看到规则引擎配置为当匹配条件（设备类型）为真时将用户发送到站点的移动版本。 

![路由配置替代](./media/front-door-rules-engine/rules-engine-architecture-1.png)

在这两个示例中，当不满足任何匹配条件时，会执行指定的路由规则。 

## <a name="terminology"></a>术语 

使用 AFD 规则引擎，可以创建规则引擎配置的组合，每个配置由一组规则组成。 下面概述了在配置规则引擎时会遇到的一些有用的术语。 

- 规则引擎配置：应用于单个路由规则的一组规则。 每个配置限制为 25 个规则。 最多可以创建 10 个配置。 
- 规则引擎规则：由最多 10 个匹配条件和 5 个操作组成的规则。
- 匹配条件：可以利用许多匹配条件来分析传入请求。 一个规则最多可包含 10 个匹配条件。 将使用 AND 运算符来评估匹配条件。 可以在[此处](front-door-rules-engine-match-conditions.md)找到匹配条件的完整列表。 
- *操作*：操作规定如何处理传入请求 - 目前，请求/响应标头操作、转发、重定向和重写都是可以使用的操作。 一个规则最多可包含 5 个操作；但是，一个规则只能包含 1 个路由配置替代。  可以在[此处](front-door-rules-engine-actions.md)找到操作的完整列表。


## <a name="next-steps"></a>后续步骤

- 了解如何配置你的第一个[规则引擎配置](front-door-tutorial-rules-engine.md)。 
- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
