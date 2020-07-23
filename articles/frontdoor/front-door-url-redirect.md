---
title: Azure 前端-URL 重定向 |Microsoft Docs
description: 本文将帮助你了解 Azure 前门如何支持其路由的 URL 重定向（如果已配置）。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80295474"
---
# <a name="url-redirect"></a>URL 重定向
可以使用 Azure 前门来重定向流量。 可以重定向多个级别（协议、主机名、路径、查询字符串）的流量，并为单独的微服务配置所有功能，因为重定向是基于路径的。 这样可以简化应用程序配置、优化资源使用情况，并支持全局重定向和基于路径的重定向等新的重定向方案。
</br>

![Azure 前门 URL 重定向][1]

## <a name="redirection-types"></a>重定向类型
重定向类型为客户端设置响应状态代码，以了解重定向的目的。 支持以下类型的重定向：

- **301 （永久移动）**：指示已为目标资源分配了新的永久 URI，以后对此资源的任何引用都应使用其中一个包含的 uri。 将301状态代码用于 HTTP 到 HTTPS 的重定向。 
- **302 （找到）**：指示目标资源临时驻留在其他 URI 下。 由于有时可能会更改重定向，因此客户端应继续使用有效的请求 URI 来处理将来的请求。
- **307 （临时重定向）**：指示目标资源临时驻留在其他 uri 下，并且用户代理执行到该 uri 的自动重定向时，不得更改请求方法。 由于重定向可能会随时间而变化，因此客户端应继续使用原始的有效请求 URI 来处理将来的请求。
- **308 （永久重定向）**：指示目标资源已分配新的永久 URI，以后对此资源的任何引用都应使用其中一个包含的 uri。 具有链接编辑功能的客户端应将对有效请求 URI 的引用自动重新链接到服务器发送的一个或多个新引用（如果可能）。

## <a name="redirection-protocol"></a>重定向协议
你可以设置将用于重定向的协议。 这允许重定向功能最常见的用例之一是将 HTTP 设置为 HTTPS 重定向。

- **仅限 https**：如果想要将流量从 HTTP 重定向到 https，请将协议设置为 "仅限 https"。 Azure 前门建议你始终将重定向设置为 "仅限 HTTPS"。
- **仅限 http**：这会将传入请求重定向到 http。 仅当你想要将流量 HTTP 保持为非加密时，才使用此值。
- **匹配请求**：此选项保留传入请求使用的协议。 因此，HTTP 请求将保持 HTTP，HTTPS 请求将保持 HTTPS post 重定向。

## <a name="destination-host"></a>目标主机
在配置重定向路由的过程中，还可以更改重定向请求的主机名或域。 你可以设置此字段以更改重定向的 URL 中的主机名，或通过其他方式保留传入请求中的主机名。 因此，使用此字段可以将发送到的所有请求重定向 `https://www.contoso.com/*` 到 `https://www.fabrikam.com/*` 。

## <a name="destination-path"></a>目标路径
如果要将 URL 的路径段替换为重定向的一部分，则可以使用新的 "路径" 值来设置此字段。 否则，你可以选择保留路径值作为重定向的一部分。 因此，使用此字段可以将发送到的所有请求重定向 `https://www.contoso.com/\*` 到 `https://www.contoso.com/redirected-site` 。

## <a name="query-string-parameters"></a>查询字符串参数
还可以替换重定向 URL 中的查询字符串参数。 为了替换传入请求 URL 中的任何现有查询字符串，请将此字段设置为 "Replace"，并设置相应的值。 否则，可以通过将字段设置为 "保留" 来保留原始查询字符串集。 例如，使用此字段可以将发送到的所有流量重定向 `https://www.contoso.com/foo/bar` 到 `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` 。 

## <a name="destination-fragment"></a>目标片段
目标片段是 "#" 之后的 URL 部分，浏览器通常使用该部分在页面上的特定部分中进行陆地。 您可以设置此字段，以便将片段添加到重定向 URL。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png